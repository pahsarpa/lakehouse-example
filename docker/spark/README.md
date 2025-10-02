# Spark-Docker

---
Папка для сборки Spark образа 

---

Пример команды сборки образа:
```bash
# from project root
cd docker/spark
docker build -t lakehouse-example/spark:latest .
```

Проверка работы образа (ожидается, что в сети dar-framework есть контейнеры platform-s3-data-minio, iceberg-jdbc-db)

cmd:
```bash
docker run -it --rm  lakehouse-example/spark:latest spark-sql
```