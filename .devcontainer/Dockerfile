# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.177.0/containers/java-8/.devcontainer/base.Dockerfile
FROM mcr.microsoft.com/vscode/devcontainers/java:0-8

ARG HTTP_PROXY_HOST=""
ARG HTTP_PROXY_PORT=""
ENV http_proxy=${HTTP_PROXY_PORT:+http://${HTTP_PROXY_HOST}:${HTTP_PROXY_PORT}}
ENV https_proxy=${HTTP_PROXY_PORT:+http://${HTTP_PROXY_HOST}:${HTTP_PROXY_PORT}}

# [Option] Install Maven
ARG INSTALL_MAVEN="false"
ARG MAVEN_VERSION=""
# [Option] Install Gradle
ARG INSTALL_GRADLE="false"
ARG GRADLE_VERSION=""
# Sbt version
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

# Proxy settings for Bloop
USER vscode
RUN if [ "${HTTP_PROXY_HOST}" != "" ]; then echo "-Dhttps.proxyHost=${HTTP_PROXY_HOST} \n\
-Dhttps.proxyPort=${HTTP_PROXY_PORT} \n\
-Dhttp.proxyHost=${HTTP_PROXY_HOST} \n\
-Dhttp.proxyPort=${HTTP_PROXY_PORT}" > /tmp/.jvmopts && mv /tmp/.jvmopts /home/vscode/.bloop/; fi

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>
