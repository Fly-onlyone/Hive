# Môi trường triển khai Hive bằng Docker

## Giới thiệu

Repo này được tạo ra để mô phỏng một môi trường dữ liệu cơ bản, giúp tôi:

- tìm hiểu cách triển khai Hadoop và Hive bằng Docker
- cấu hình Hive Metastore
- kết nối Hive Server để truy vấn dữ liệu
- chuẩn bị nền tảng để làm việc với các bài toán lưu trữ và phân tích dữ liệu lớn


## Thành phần chính

- **NameNode**
- **DataNode**
- **Hive Metastore**
- **Hive Server**
- **PostgreSQL** dùng làm metastore database
- **Trino config** để mở rộng khả năng truy vấn

## Công nghệ sử dụng

- Docker
- Docker Compose
- Hadoop HDFS
- Apache Hive
- PostgreSQL
- Trino

## Cấu trúc thư mục

```bash
.
├── docker-compose.yaml
├── hadoop-hive.env
├── hive-site.xml
├── shared-data/
└── trino-config/
    ├── config.properties
    ├── jvm.config
    └── node.properties
