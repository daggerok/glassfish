# Glassfish [![Build Status](https://travis-ci.org/daggerok/glassfish.svg?branch=master)](https://travis-ci.org/daggerok/glassfish)
automated build for docker hub

## Glassfish 5

**Java EE 8**

*tags based on `openjdk:8u151-jdk-alpine` image*

- latest
- 5.0 (full profile)
- 5.0-web (web profile)

**Exposed ports**:

- 8080 - deployed apps http port

### Usage (with healthcheck):

```

FROM daggerok/glassfish:5.0-web
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
