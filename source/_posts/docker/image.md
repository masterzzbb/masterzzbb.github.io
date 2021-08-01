---
title: Docker Image
categories:
- Docker Image
tags: 
- Docker
---

## Docker Image

* build docker image

**不同类型的项目的Dockerfile内容会有所差异**


```shell
FROM 
MAINTAINER calabash
ADD target/e-travel-core-deploy-bin.zip /root/
WORKDIR /root
RUN unzip -oq e-travel-core-deploy-bin.zip
ENV JAVA_OPTS="$JAVA_OPTS -Dfile.encoding=UTF8 -Duser.timezone=GMT+08 -Dspring.profiles.active=dev"
ENTRYPOINT ["/bin/sh","-c","java -jar $JAVA_OPTS /root/e-travel-core-deploy/e-travel-core-deploy.jar"]
```