# Hadoop + Hive bằng Docker Compose

Môi trường Hadoop HDFS 2.7.4 + Apache Hive 2.3.2 (metastore trên PostgreSQL) chạy trên một máy,
dùng làm tầng lưu trữ cho đồ án [BigData – Hệ thống dự đoán mức nguy cơ ung thư phổi](https://github.com/Fly-onlyone/BigData).

> Cấu hình này đã được gộp vào thư mục [`docker/`](https://github.com/Fly-onlyone/BigData/tree/master/docker)
> của repo BigData, để cả hệ thống chạy được từ một repo. Repo này giữ lại làm tham khảo.

## Thành phần

| Container | Image | Vai trò | Cổng trên máy |
|---|---|---|---|
| `namenode` | bde2020/hadoop-namenode (Hadoop 2.7.4) | Quản lý metadata HDFS | 8020, web UI 51070 |
| `datanode` | bde2020/hadoop-datanode | Lưu block dữ liệu HDFS | web UI 51075 |
| `hive-metastore-postgresql` | bde2020/hive-metastore-postgresql | Database của metastore | – |
| `hive-metastore` | bde2020/hive:2.3.2-postgresql-metastore | Dịch vụ metastore (Thrift) | 9083 |
| `hive-server` | bde2020/hive:2.3.2-postgresql-metastore | HiveServer2, nhận truy vấn HiveQL | 10000 |

`hive-site.xml` bật transaction (ACID: `DbTxnManager`, compactor) để bảng ORC `transactional=true`
nhận được lệnh `INSERT` từng dòng từ ứng dụng.

## Cách chạy

```bash
docker compose up -d
docker compose ps            # đợi khoảng 1-2 phút cho hive-server sẵn sàng
docker exec -it hive-server /opt/hive/bin/beeline -u jdbc:hive2://localhost:10000 -e "SHOW DATABASES;"
```

Thư mục `shared-data/` được mount vào `hive-server` tại `/data`, nên có thể nạp file bằng
`LOAD DATA LOCAL INPATH '/data/Cancer.csv' ...`.

Thứ tự khởi động do biến `SERVICE_PRECONDITION` điều khiển:
namenode → datanode → metastore (cần PostgreSQL) → hive-server.

Tài khoản `hive/hive` của metastore chỉ dùng cho môi trường chạy thử trên máy cá nhân.

## Cấu trúc

```text
.
├── docker-compose.yaml
├── hadoop-hive.env      # cấu hình Hadoop/Hive truyền vào container qua biến môi trường
├── hive-site.xml        # metastore URI, warehouse, ACID
└── shared-data/
    └── Cancer.csv       # dữ liệu mẫu của đồ án BigData
```
