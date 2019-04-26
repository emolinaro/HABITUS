FROM openjdk:8-jre-slim

ARG BUILD_DATE
ARG SPARK_VERSION=2.4.2
ARG HABITUS_VERSION=1.0

MAINTAINER Emiliano Molinaro <emil.molinaro@gmail.com>

LABEL org.label-schema.name="HABITUS v$HABITUS_VERSION" \
      org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.version=$HABITUS_VERSION \
      authors="Emiliano Molinaro" \
      description="HABITUS is an implementation of the Personal Activity and Location Measurement System (PALMS),\
written in Python and integrated with Apache Spark for cluster-computing.\
The program detects personal activity patterns of individual participants wearing\
a GPS data logger and a physical activity monitor."

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
    pip install --upgrade pip && \
    pip install --no-cache pyspark==${SPARK_VERSION} && \
    SPARK_HOME=$(python /opt/miniconda3/bin/find_spark_home.py) && \
    echo "export SPARK_HOME=$(python /opt/miniconda3/bin/find_spark_home.py)" > /etc/profile.d/spark.sh && \
    curl -s --url "http://central.maven.org/maven2/com/amazonaws/aws-java-sdk/1.7.4/aws-java-sdk-1.7.4.jar" --output $SPARK_HOME/jars/aws-java-sdk-1.7.4.jar && \
    curl -s --url "http://central.maven.org/maven2/org/apache/hadoop/hadoop-aws/2.7.3/hadoop-aws-2.7.3.jar" --output $SPARK_HOME/jars/hadoop-aws-2.7.3.jar && \
    mkdir -p $SPARK_HOME/conf && \
    echo "spark.hadoop.fs.s3.impl=org.apache.hadoop.fs.s3a.S3AFileSystem" >> $SPARK_HOME/conf/spark-defaults.conf && \
    apt-get remove -y curl bzip2 && \
    apt-get autoremove -y && \
    apt-get clean && \
    mkdir /work

WORKDIR /work

COPY requirements.txt requirements.txt
COPY habitus-${HABITUS_VERSION}-py3-none-any.whl habitus-${HABITUS_VERSION}-py3-none-any.whl

RUN pip install -r requirements.txt && \
    $PYSPARK_PYTHON -m pip install habitus-${HABITUS_VERSION}-py3-none-any.whl && \
    rm *.txt *.whl

#ENTRYPOINT ["spark-submit"]
ENTRYPOINT ["habitus"]
CMD ["--help"]