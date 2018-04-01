# Glassfish [![Build Status](https://travis-ci.org/daggerok/glassfish.svg?branch=master)](https://travis-ci.org/daggerok/glassfish)
automated build for docker hub

**Java EE 8**

- [5.0](https://github.com/daggerok/glassfish/blob/5.0/Dockerfile) (full profile based on [`openjdk:8u151-jdk-alpine` image](https://hub.docker.com/_/openjdk/) + JCE installed)
- [5.0-web](https://github.com/daggerok/glassfish/blob/5.0-web/Dockerfile) (web profile based on [`openjdk:8u151-jdk-alpine` image](https://hub.docker.com/_/openjdk/) + JCE installed)

**Exposed ports**:

- 8080 - deployed web apps
- 4848 - management console
- 5005 - remote debug

### Usage (with health-check):

```

FROM daggerok/glassfish:5.0
HEALTHCHECK --timeout=2s --retries=22 \
        CMD wget -q --spider http://127.0.0.1:8080/my-service/health \
         || exit 1
ADD ./build/libs/*.war ${GLASSFISH_HOME}/glassfish/domains/domain1/autodeploy/my-service.war

```

#### Remote debug / multi-build deployment:

```

FROM daggerok/glassfish:5.0-web
# Remote debug:
ENV JAVA_OPTS="$JAVA_OPTS -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005 "
EXPOSE 5005
# Multi-builds deployment:
COPY ./build/libs/*.war ./target/*.war ${GLASSFISH_HOME}/glassfish/domains/domain1/autodeploy/

```

### setup JMX console administration password

**bash command**

```

docker run --rm --name glassfish -d -p 4848:4848 -e GLASSFISH_ADMIN_PASSWORD="Admin.123" daggerok/glassfish

```

**Dockerfile**

```

FROM daggerok/glassfish:5.0-web
ENV GLASSFISH_ADMIN_PASSWORD=Admin.123

```

**docker-compose**

```

version: "2.1"

services:
  my-service:
    image: daggerok/glassfish:5.0
    ports:
    - "5005:5005"
    - "8080:8080"
    - "4848:4848"

```
