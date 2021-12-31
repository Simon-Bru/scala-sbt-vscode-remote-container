# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.177.0/containers/java-8/.devcontainer/base.Dockerfile
FROM mcr.microsoft.com/vscode/devcontainers/java:0-8

ARG HTTP_PROXY_HOST=""
ARG HTTP_PROXY_PORT=""
ENV http_proxy=${HTTP_PROXY_PORT:+http://${HTTP_PROXY_HOST}:${HTTP_PROXY_PORT}}
ENV https_proxy=${HTTP_PROXY_PORT:+http://${HTTP_PROXY_HOST}:${HTTP_PROXY_PORT}}

# Increase JVM memory limits
ENV JAVA_OPTS='-Xms2048m -Xmx8g'

# [Option] Install Maven
ARG INSTALL_MAVEN="false"
ARG MAVEN_VERSION=""
# [Option] Install Gradle
ARG INSTALL_GRADLE="false"
ARG GRADLE_VERSION=""
# [Option] Install Sbt
ARG INSTALL_SBT="true"
ARG SBT_VERSION=""

RUN if [ "${INSTALL_MAVEN}" = "true" ]; then su vscode -c "umask 0002 && . /usr/local/sdkman/bin/sdkman-init.sh && sdk install maven \"${MAVEN_VERSION}\""; fi \
    && if [ "${INSTALL_GRADLE}" = "true" ]; then su vscode -c "umask 0002 && . /usr/local/sdkman/bin/sdkman-init.sh && sdk install gradle \"${GRADLE_VERSION}\""; fi \
    && if [ "${INSTALL_SBT}" = "true" ]; then su vscode -c "umask 0002 && . /usr/local/sdkman/bin/sdkman-init.sh && sdk install sbt \"${SBT_VERSION}\""; fi

# Make sure vscode extensions folder are owned by user vscode
RUN mkdir -p /home/vscode/.vscode-server/extensions \
        /home/vscode/.vscode-server-insiders/extensions \
        /home/vscode/.bloop \
    && chown -R vscode \
        /home/vscode/.bloop \
        /home/vscode/.vscode-server \
        /home/vscode/.vscode-server-insiders

# APT proxy settings
RUN if [ "${HTTP_PROXY_HOST}" != "" ]; then echo "Acquire::http::proxy \"http://${HTTP_PROXY_HOST}:${HTTP_PROXY_PORT}\";\n\
Acquire::https::proxy \"http://${HTTP_PROXY_HOST}:${HTTP_PROXY_PORT}\";\n\
Acquire::ftp::proxy \"http://${HTTP_PROXY_HOST}:${HTTP_PROXY_PORT}\";" > /etc/apt/apt.conf.d/proxy.conf; fi

# Install Spark-Shell
ARG INSTALL_SPARK=false
ARG SPARK_VERSION=3.1.2
ARG HADOOP_VERSION=3.2
ARG DELTA_VERSION=1.1.0
RUN if [ "${INSTALL_SPARK}" != "" ]; then apt-get update \
    && wget https://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
    && tar -xvzf spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
    && mv spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}/ /opt/spark \
    && rm spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz; fi

ENV SPARK_HOME=/opt/spark
ENV PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin

# SBT Java version setting file
RUN mkdir /etc/sbt && echo "-java-home\n\
\$JAVA_HOME" > /etc/sbt/sbtopts

# Proxy settings for Bloop
USER vscode
RUN if [ "${HTTP_PROXY_HOST}" != "" ]; then echo "-Dhttps.proxyHost=${HTTP_PROXY_HOST} \n\
-Dhttps.proxyPort=${HTTP_PROXY_PORT} \n\
-Dhttp.proxyHost=${HTTP_PROXY_HOST} \n\
-Dhttp.proxyPort=${HTTP_PROXY_PORT}" > /home/vscode/.bloop/.jvmopts; fi

# Proxy for Spark Shell (Delta package download)
RUN echo "spark.sql.catalog.spark_catalog org.apache.spark.sql.delta.catalog.DeltaCatalog \n\
spark.sql.extensions io.delta.sql.DeltaSparkSessionExtension \n\
spark.jars.packages io.delta:delta-core_2.12:${DELTA_VERSION}" > $SPARK_HOME/conf/spark-defaults.conf
RUN if [ "${HTTP_PROXY_HOST}" != "" ]; \
    then echo "spark.driver.extraJavaOptions -Dhttps.proxyHost=${HTTP_PROXY_HOST} -Dhttps.proxyPort=${HTTP_PROXY_PORT} -Dhttp.proxyHost=${HTTP_PROXY_HOST} -Dhttp.proxyPort=${HTTP_PROXY_PORT} \n"\
    > $SPARK_HOME/conf/spark-defaults.conf; \
  fi
