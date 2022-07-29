FROM apache/spark:v3.2.1

USER root

RUN apt-get update && \
    apt install -y nfs-common

WORKDIR /opt/spark/work-dir
COPY jars /opt/spark/jars

ARG spark_uid=185

# Specify the User that the actual main process will run as
USER ${spark_uid}