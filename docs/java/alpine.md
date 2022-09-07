# 制作 Java Docker 基础镜像

> [Alpine Packages 搜索 tzdata 安装包。](https://pkgs.alpinelinux.org/packages)  
> [AlpineLinux 中安装 glibc 步骤。](http://blog.fpliu.com/it/software/GNU/glibc#alpine)

## 1 Dockerfile

&emsp;&emsp;*由于制作的是一个最小 Java 环境镜像，请先准备 `jre-8u202-linux-x64.tar.gz` JRE 安装包。*

```dockerfile
FROM alpine:3.14.0

MAINTAINER Aaric

ENV LANG=zh_CN.UTF-8 \
  LC_ALL=zh_CN.UTF-8 \
  JAVA_HOME=/usr/java/jre1.8.0_202 \
#  CLASSPATH=.:${JAVA_HOME}/lib \
#  PATH=${JAVA_HOME}/bin:${PATH}
  CLASSPATH=.:/usr/java/jre1.8.0_202/lib \
  PATH=${PATH}:/usr/java/jre1.8.0_202/bin

RUN apk add --no-cache tzdata curl \
    && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone \
    && apk del tzdata

RUN apk add --no-cache ca-certificates \
  && curl -L -o /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub \
  && curl -LO https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.32-r0/glibc-2.32-r0.apk \
  && curl -LO https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.32-r0/glibc-bin-2.32-r0.apk \
  && curl -LO https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.32-r0/glibc-dev-2.32-r0.apk \
  && curl -LO https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.32-r0/glibc-i18n-2.32-r0.apk \
  && apk add --no-cache glibc-2.32-r0.apk \
  && apk add --no-cache glibc-bin-2.32-r0.apk \
  && apk add --no-cache glibc-dev-2.32-r0.apk \
  && apk add --no-cache glibc-i18n-2.32-r0.apk \
  && rm -f *.apk \
  && /usr/glibc-compat/bin/localedef -i zh_CN -f UTF-8 zh_CN.UTF-8

ADD jre-8u202-linux-x64.tar.gz /usr/java
```

## 2 Build

```bash
docker build -t oracle-jdk8:8u202-alpine .
docker tag oracle-jdk8:8u202-alpine oracle-jdk8:latest
docker push oracle-jdk8:8u202-alpine
docker push oracle-jdk8:latest
```

## 3 Testing

```bash
docker pull oracle-jdk8:latest
docker run --name jdk8 -it oracle-jdk8:latest java -version
```
