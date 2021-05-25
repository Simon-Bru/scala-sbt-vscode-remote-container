# scala-sbt-vscode-remote-container
A remote container for working with Scala in Visual Studio Code without package installation/configuration headache

# Services installation

You can install following dependency managers:
- [SBT](https://github.com/sbt/sbt): Installed by default; controlled by argument `INSTALL_SBT`
- [Maven](https://github.com/apache/maven): To install, set variable `INSTALL_MAVEN` to true in [devcontainer.json](.devcontainer/devcontainer.json)
- [Gradle](https://github.com/gradle/gradle): To install, set variable `INSTALL_GRADLE` to true in [devcontainer.json](.devcontainer/devcontainer.json)
- [Spark](https://github.com/apache/spark): To install Spark locally (spark-shell...), set variable `INSTALL_SPARK` to true in [devcontainer.json](.devcontainer/devcontainer.json)

# Corporate proxy

> **_INFO:_** To workaround corporate VPN; you must have some kind of proxy setup on your machine (ex: [Cntlm](https://github.com/versat/cntlm)).
That proxy must be running to run the development container.

To enable proxy usage for the remote env, add the following args to [devcontainer.json](.devcontainer/devcontainer.json):

1. `"HTTP_PROXY_HOST": "host.docker.internal"`. This is the default proxy that docker uses internally.
2. `"HTTP_PROXY_PORT": "3128"`

You also need to specify those settings for VSCode's [metals](https://github.com/scalameta/metals) extension:

```
"http.proxy": "http://host.docker.internal:3128",
"metals.serverProperties": [
    "-Dhttps.proxyHost=host.docker.internal",
    "-Dhttps.proxyPort=3128",
    "-Dhttp.proxyHost=host.docker.internal",
    "-Dhttp.proxyPort=3128"
],
```