FROM openjdk:8-jdk-slim

ARG BUILD_DATE=12.12.2019

MAINTAINER Emiliano Molinaro <emil.molinaro@gmail.com>

LABEL org.label-schema.name="HABITUS v1.9.4" \
      org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.version=1.9.4 \
      authors="Emiliano Molinaro" \
      description="HABITUS is an implementation of the Personal Activity and Location Measurement System (PALMS),\
written in Python and integrated with Apache Spark for cluster-computing.\
The program detects personal activity patterns of individual participants wearing\
a GPS data logger and a physical activity monitor."

ARG SPARK_VERSION=2.4.2
ENV PATH="/opt/miniconda3/bin:${PATH}"
ENV PYSPARK_PYTHON="/opt/miniconda3/bin/python"

RUN apt-get update && \
    apt-get install -y curl bzip2 --no-install-recommends && \
    apt-get install -y libblas-dev libatlas-base-dev && \
    curl -s --url "https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh" --output /tmp/miniconda.sh && \
    bash /tmp/miniconda.sh -b -f -p "/opt/miniconda3" && \
    rm /tmp/miniconda.sh && \
    conda config --set auto_update_conda true && \
    conda config --set channel_priority false && \
    conda update conda -y --force && \
    conda clean -tipsy && \
    echo "PATH=/opt/miniconda3/bin:\${PATH}" > /etc/profile.d/miniconda.sh && \
    python -m pip install --no-cache --upgrade pip && \
    python -m pip install --no-cache pyspark==${SPARK_VERSION} && \
    SPARK_HOME=$(python /opt/miniconda3/bin/find_spark_home.py) && \
    echo "export SPARK_HOME=$(python /opt/miniconda3/bin/find_spark_home.py)" > /etc/profile.d/spark.sh && \
    curl -s --url "https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk/1.7.4/aws-java-sdk-1.7.4.jar" --output $SPARK_HOME/jars/aws-java-sdk-1.7.4.jar && \
    curl -s --url "https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk/2.7.3/hadoop-aws-2.7.3.jar" --output $SPARK_HOME/jars/hadoop-aws-2.7.3.jar && \
    mkdir -p $SPARK_HOME/conf && \
    echo "spark.hadoop.fs.s3.impl=org.apache.hadoop.fs.s3a.S3AFileSystem" >> $SPARK_HOME/conf/spark-defaults.conf && \
    apt-get remove -y curl bzip2 && \
    apt-get autoremove -y && \
    apt-get clean && \
    mkdir /work

WORKDIR /work

COPY environment.yml environment.yml
COPY wheels/habitus-1.9.4-py3-none-any.whl habitus-1.9.4-py3-none-any.whl

RUN sed -i -e "s/x.y.z/1.9.4/g" environment.yml && \
    conda env update -n base --file environment.yml && \
    conda clean -tipsy && \
    rm *.yml* *.whl

# ENTRYPOINT ["spark-submit"]
# ENTRYPOINT ["habitus"]
# CMD ["--help"]
