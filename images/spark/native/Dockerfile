FROM apache/spark:v3.2.2
FROM apache/spark-py:v3.2.2

USER root

# Make sure we install LDAP
RUN echo "$LOG_TAG install LDAP packages" && \
    apt-get -y update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y libnss-ldap libpam-ldap ldap-utils wget

# Install conda to manage python and R packages
ARG miniconda_version="py38_4.8.3"
ARG miniconda_sha256="879457af6a0bf5b34b48c12de31d4df0ee2f06a8e68768e5758c3293b2daf688"
# Install python and R packages via conda
COPY ./images/zeppelin/bin/env_python_3_with_R.yml /env_python_3_with_R.yml

RUN rm -rf /opt/conda && \
    set -ex && \
    wget -nv https://repo.anaconda.com/miniconda/Miniconda3-${miniconda_version}-Linux-x86_64.sh -O miniconda.sh && \
    echo "${miniconda_sha256} miniconda.sh" > anaconda.sha256 && \
    sha256sum --strict -c anaconda.sha256 && \
    bash miniconda.sh -b -p /opt/conda && \
    export PATH=/opt/conda/bin:$PATH && \
    conda config --set always_yes yes --set changeps1 no && \
    conda info -a && \
    conda install mamba -c conda-forge && \
    mamba env update -f /env_python_3_with_R.yml --prune && \
    # Cleanup
    rm -v miniconda.sh anaconda.sha256  && \
    # Cleanup based on https://github.com/ContinuumIO/docker-images/commit/cac3352bf21a26fa0b97925b578fb24a0fe8c383
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    mamba clean -ay && \
    chmod -R 775 /opt/conda

ENV PATH /opt/conda/envs/python_3_with_R/bin:/opt/conda/bin:$PATH

# setup LDAP access for Spark
COPY ./images/zeppelin/bin/ldap.conf /etc/ldap/
COPY ./images/zeppelin/bin/ldap2.conf /etc/ldap.conf
COPY ./images/zeppelin/bin/nsswitch.conf /etc/
RUN   echo "session required    pam_mkhomedir.so skel=/etc/skel umask=0022" >> /etc/pam.d/common-session

RUN echo -n | openssl s_client -connect ldaps.iscpif.fr:636 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /etc/ldap/ca_certs.pem && \
    keytool -import -keystore $JAVA_HOME/lib/security/cacerts -storepass changeit -noprompt -alias mycert -file /etc/ldap/ca_certs.pem && \
    getent passwd

RUN getent passwd

WORKDIR /opt/spark/work-dir
COPY ./images/spark/native/jars /opt/spark/jars

ARG spark_uid=185
# Specify the User that the actual main process will run as
USER ${spark_uid}

