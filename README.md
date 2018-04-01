# Glassfish [![Build Status](https://travis-ci.org/daggerok/glassfish.svg?branch=master)](https://travis-ci.org/daggerok/glassfish)
automated build for docker hub

**Java EE 8**

- [5.0](https://github.com/daggerok/glassfish/blob/5.0/Dockerfile) (full profile based on [`openjdk:8u151-jdk-alpine` image](https://hub.docker.com/_/openjdk/) + JCE installed)
- [5.0-web](https://github.com/daggerok/glassfish/blob/5.0-web/Dockerfile) (web profile based on [`openjdk:8u151-jdk-alpine` image](https://hub.docker.com/_/openjdk/) + JCE installed)

**Exposed ports**:

- 8080 - deployed web apps
- 4848 - management console
- 5005 - remote debug

## Usage

### Health-check

assuming your application `my-app` has implemented `/api/health` endpoint:

```

FROM daggerok/glassfish:5.0
HEALTHCHECK --timeout=2s --retries=22 \
        CMD wget -q --spider http://127.0.0.1:8080/my-app/api/health \
         || exit 1
ADD ./build/libs/*.war ${GLASSFISH_HOME}/glassfish/domains/domain1/autodeploy/my-app.war

```

### Remote debug / Multi-build deployment

```

FROM daggerok/glassfish:5.0-web
# Remote debug:
ENV JAVA_OPTS="$JAVA_OPTS -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005 "
EXPOSE 5005
# Multi-builds deployment:
COPY ./build/libs/*.war ./target/*.war ${GLASSFISH_HOME}/glassfish/domains/domain1/autodeploy/

```

## Setup management console admin password

### when running docker using bash command

```

docker run --rm --name glassfish -d -p 4848:4848 -e GLASSFISH_ADMIN_PASSWORD=Admin.123 daggerok/glassfish

```

### via Dockerfile

```

FROM daggerok/glassfish:5.0-web
ENV GLASSFISH_ADMIN_PASSWORD="Admin.123"

```
