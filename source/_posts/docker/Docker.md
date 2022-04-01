---
title: Docker Image
categories:
- Docker Image
tags: 
- Docker
---

# Docker

## image

* **build docker image**

  **Dockerfile**

  ```dockerfile
  # 基础镜像
  FROM openjdk:8-jdk-alpine
  
  # 作者信息
  MAINTAINER "xxx xxxxx@qq.com"
  
  # 添加一个存储空间
  VOLUME /tmp
  
  # 暴露8888端口
  EXPOSE 8888
  
  # 添加变量，如果使用dockerfile-maven-plugin，则会自动替换这里的变量内容
  ARG JAR_FILE=target/spring-boot-demo-docker.jar
  
  # 往容器中添加jar包
  ADD ${JAR_FILE} app.jar
  
  # 启动镜像自动运行程序
  ENTRYPOINT ["java","-Djava.security.egd=file:/dev/urandom","-jar","/app.jar"]
  ```

​				**打包方式**

1. 手动打包

> 前往 Dockerfile 目录，打开命令行执行

```shell
docker build -t spring-boot-demo-docker .
```

2. maven插件打包

> pom.xml 中添加插件

```xml
<properties>
    <dockerfile-version>1.4.9</dockerfile-version>
</properties>

<plugins>      
    <plugin>
        <groupId>com.spotify</groupId>
        <artifactId>dockerfile-maven-plugin</artifactId>
        <version>${dockerfile-version}</version>
        <configuration>
            <repository>${project.build.finalName}</repository>
            <tag>${project.version}</tag>
            <buildArgs>
                <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
            </buildArgs>
        </configuration>
        <executions>
            <execution>
                <id>default</id>
                <phase>package</phase>
                <goals>
                    <goal>build</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
</plugins>
```

> 执行mvn打包命令，因为插件中 `execution` 节点配置了 package，所以会在打包的时候自动执行 build 命令

```shell
mvn clean package -Dmaven.test.skip=true
```

