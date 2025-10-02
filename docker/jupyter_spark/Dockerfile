# Базовый образ для Jupyter
FROM jupyter/minimal-notebook:python-3.10

# Версии Spark и Hadoop
ENV SPARK_VERSION=3.4.0
ENV SPARK_MAJOR_VERSION=3.4
#ENV HADOOP_VERSION=3.3.4
ENV ICEBERG_VERSION=1.9.1
ENV HIVE_VERSION=3.1.3

ENV SPARK_HOME=/opt/spark
#ENV HADOOP_HOME=/opt/hadoop-$HADOOP_VERSION
#ENV HADOOP_CONF_DIR=/etc/hadoop
ENV HIVE_HOME=/opt/hive
ENV MULTIHOMED_NETWORK=1
#ENV LD_LIBRARY_PATH=/home/hadoop/hadoop/lib/native
ENV PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
#:$HADOOP_HOME/bin

# Переключаемся на пользователя root для установки пакетов
USER root

# Обновляем систему и устанавливаем curl
RUN apt-get update && apt-get install -y --no-install-recommends \
    openjdk-17-jdk \
    curl \
    wget \
    unzip \
    ca-certificates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*


# Скачиваем и устанавливаем Hive
RUN wget -qO- https://archive.apache.org/dist/hive/hive-${HIVE_VERSION}/apache-hive-${HIVE_VERSION}-bin.tar.gz | tar -xz -C /usr/local \
    && mv /usr/local/apache-hive-${HIVE_VERSION}-bin /opt/hive

# Скачиваем и устанавливаем Spark
RUN wget http://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop3.tgz \
    && tar zxvf spark-${SPARK_VERSION}-bin-hadoop3.tgz \
    && rm spark-${SPARK_VERSION}-bin-hadoop3.tgz \
    && mv spark-${SPARK_VERSION}-bin-hadoop3 /opt/spark


# Создаем директорию для JAR-файлов и скачиваем JDBC драйверы
RUN wget https://jdbc.postgresql.org/download/postgresql-42.7.3.jar -P /opt/spark/jars/ \
    && wget https://github.com/microsoft/mssql-jdbc/releases/download/v12.6.3/mssql-jdbc-12.6.3.jre11.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/3.3.2/hadoop-aws-3.3.2.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/1.11.1026/aws-java-sdk-bundle-1.11.1026.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/wildfly/openssl/wildfly-openssl/1.0.7.Final/wildfly-openssl-1.0.7.Final.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/spark/spark-sql-kafka-0-10_2.12/3.4.0/spark-sql-kafka-0-10_2.12-3.4.0.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/spark/spark-token-provider-kafka-0-10_2.12/3.4.0/spark-token-provider-kafka-0-10_2.12-3.4.0.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/kafka/kafka-clients/3.3.2/kafka-clients-3.3.2.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/com/google/code/findbugs/jsr305/3.0.0/jsr305-3.0.0.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/commons/commons-pool2/2.11.1/commons-pool2-2.11.1.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-client-runtime/3.3.4/hadoop-client-runtime-3.3.4.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/lz4/lz4-java/1.8.0/lz4-java-1.8.0.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/xerial/snappy/snappy-java/1.1.9.1/snappy-java-1.1.9.1.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/slf4j/slf4j-api/2.0.6/slf4j-api-2.0.6.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-client-api/3.3.4/hadoop-client-api-3.3.4.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/commons-logging/commons-logging/1.1.3/commons-logging-1.1.3.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/spark/spark-avro_2.12/3.4.0/spark-avro_2.12-3.4.0.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/tukaani/xz/1.9/xz-1.9.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/io/delta/delta-core_2.12/2.4.0/delta-core_2.12-2.4.0.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/io/delta/delta-storage/2.4.0/delta-storage-2.4.0.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/antlr/antlr4-runtime/4.9.3/antlr4-runtime-4.9.3.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/com/databricks/spark-xml_2.12/0.18.0/spark-xml_2.12-0.18.0.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/iceberg/iceberg-spark-runtime-${SPARK_MAJOR_VERSION}_2.12/${ICEBERG_VERSION}/iceberg-spark-runtime-${SPARK_MAJOR_VERSION}_2.12-${ICEBERG_VERSION}.jar -P /opt/spark/jars/ \
    && wget https://repo1.maven.org/maven2/org/apache/iceberg/iceberg-aws-bundle/${ICEBERG_VERSION}/iceberg-aws-bundle-${ICEBERG_VERSION}.jar -P /opt/spark/jars/


# Настраиваем SPARK_CLASSPATH, чтобы включить все JAR-файлы из /opt/spark/jars/
ENV SPARK_CLASSPATH=/opt/spark/jars
ENV IJAVA_CLASSPATH=/opt/spark/jars/*


# Скачиваем и устанавливаем AWS CLI
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-2.10.4.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install \
    && rm awscliv2.zip

# Устанавливаем необходимые пакеты и зависимости
COPY requirements.txt /
RUN pip install --no-cache-dir -r /requirements.txt

# Настроить права и пользователя
RUN chown -R ${NB_UID}:${NB_GID} /opt/spark/jars

# Настраиваем права и пользователя
USER ${NB_UID}

COPY --chown="${NB_UID}:${NB_GID}" ./ipython/startup "${HOME}/.ipython/profile_default/startup"