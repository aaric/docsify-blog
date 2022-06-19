# docsify部署与编排

## 打包镜像

```bash
cat > Dockerfile <<-'EOF'
# docsify 4.4.4 with node:16-alpine
# Author: Aaric
# Version: 4.4.4-alpine
# Build:
#   1. docker build -t ik8share/docsify:4.4.4-alpine .
#   2. docker tag ik8share/docsify:4.4.4-alpine ik8share/docsify:latest
FROM node:16-alpine

MAINTAINER Aaric

WORKDIR /app

EXPOSE 3000

RUN npm install -g docsify-cli@4.4.4 --registry=https://registry.npm.taobao.org \
  && docsify init /app/docs

ENTRYPOINT ["docsify", "serve", "docs"]
EOF

docker build -t ik8share/docsify:4.4.4-alpine .
```

## 单机编排工具

```bash
cat > docker-compose.yaml <<-'EOF'
version: "3"
services:
  hexo:
    image: ik8share/docsify:4.4.4-alpine
    container_name: docsify_community_edition
    volumes:
     - ./docs:/app/docs
    environment:
      - TZ=Asia/Shanghai
      - LANG=zh_CN.UTF-8
    ports:
      - 3000:3000
EOF
docker-compose up -d
```
