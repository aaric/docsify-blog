# MinIO

|No.|Key|Value|Remark|
|:-:|---|---|---|
|1|管理平台地址|`http://localhost:9001`||
|2|管理账号|`admin`||
|3|管理密码|`admin123`|*测试环境，请勿修改密码*|

## 1 Binary部署

### 1.1 server

```bash
# install
wget https://dl.min.io/server/minio/release/linux-amd64/minio && chmod +x ./minio

# serve
export MINIO_ROOT_USER=admin
export MINIO_ROOT_PASSWORD=admin123
#export MINIO_SERVER_ACCESS_KEY=c9193d6ae58027ae
#export MINIO_SERVER_SECRET_KEY=132b6c5fc9193d6ae58027ae302ab67b
#./minio server ./data{1,2} --console-address "0.0.0.0:9001"
./minio server ./data{1,2} --address "0.0.0.0:9000" --console-address "0.0.0.0:9001"
```

### 1.2 client

```bash
# install
wget https://dl.min.io/client/mc/release/linux-amd64/mc && chmod +x ./mc
```

## 2 DC部署

### 2.1 docker-compose.yaml

```yaml
version: "3"
services:
  minio:
    image: bitnami/minio:2022.8.13
#    image: s1:5000/bitnami/minio:2022.8.13
    container_name: minio_community_edition
    volumes:
      - minio_data:/data
    environment:
      - TZ=Asia/Shanghai
      - LANG=zh_CN.UTF-8
      - MINIO_ROOT_USER=admin
      - MINIO_ROOT_PASSWORD=admin123
#      - MINIO_SERVER_ACCESS_KEY=c9193d6ae58027ae
#      - MINIO_SERVER_SECRET_KEY=132b6c5fc9193d6ae58027ae302ab67b
    ports:
      - 9000:9000
      - 9001:9001
    restart: always
    deploy:
      restart_policy:
        condition: on-failure
volumes:
  minio_data:
    driver: local
```

### 2.2 deploy

```bash
# 初始化
docker-compose --compatibility up -d
docker-compose ps

# 销毁容器
docker-compose down -v
```
