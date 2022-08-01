FROM datamechanics/spark:3.2.1-hadoop-3.3.1-java-8-scala-2.12-python-3.8-latest

USER root

WORKDIR /opt/spark/work-dir
# COPY ./images/spark/datamechanics/jars /opt/spark/jars
ADD https://s3.amazonaws.com/auxdata.johnsnowlabs.com/public/jars/spark-nlp-assembly-4.0.2.jar /opt/spark/jars

# Install conda to manage python and R packages
ARG miniconda_version="py37_4.9.2"
# Hashes via https://docs.conda.io/en/latest/miniconda_hashes.html
ARG miniconda_sha256="79510c6e7bd9e012856e25dcb21b3e093aa4ac8113d9aa7e82a86987eabe1c31"
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

ARG spark_uid=185
# Specify the User that the actual main process will run as
USER ${spark_uid}