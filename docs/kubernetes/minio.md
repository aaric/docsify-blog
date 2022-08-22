# MinIO

|No.|Key|Value|Remark|
|:-:|---|---|---|
|1|管理平台地址|`http://localhost:9001`||
|2|管理账号|`admin`||
|3|管理密码|`admin123`|*测试环境，请勿修改密码*|
|4|普通账号|`newuser`|*仅授权`newbucket`桶管理权限*|
|5|普通密码|`newuser123`||

## 1 Binary部署

### 1.1 deploy

```bash
# install
wget https://dl.min.io/server/minio/release/linux-amd64/minio && chmod +x ./minio

# single
export MINIO_ROOT_USER=admin
export MINIO_ROOT_PASSWORD=admin123
#export MINIO_SERVER_ACCESS_KEY=fa04fe9feb245ff2
#export MINIO_SERVER_SECRET_KEY=63807858fa04fe9feb245ff21b581a9b
mkdir ./data{1..2}
#./minio server ./data{1..2} --console-address "0.0.0.0:9001"
./minio server ./data{1..2} --address "0.0.0.0:9000" --console-address "0.0.0.0:9001"
```

&emsp;&emsp;*Notes:*

```text
WARNING: MINIO_ACCESS_KEY and MINIO_SECRET_KEY are deprecated.
         Please use MINIO_ROOT_USER and MINIO_ROOT_PASSWORD
```

### 1.2 client

```bash
# install
wget https://dl.min.io/client/mc/release/linux-amd64/mc && chmod +x ./mc

# command
# https://docs.min.io/docs/minio-client-complete-guide.html
./mc config host rm local
./mc config host rm gcs
./mc config host rm s3
./mc config host add minio http://10.0.11.25:9000 admin admin123
./mc config host ls

./mc ls minio
./mc mb minio/newbucket
date > test.txt
./mc cp test.txt minio/newbucket
./mc ls minio/newbucket
./mc cat minio/newbucket/test.txt
./mc cp minio/newbucket/test.txt .
./mc rm minio/newbucket/test.txt
./mc rb --force minio/newbucket

./mc du minio
./mc admin info minio/

./mc admin user list minio
./mc admin user add minio newuser newuser123
#./mc admin user disable minio newuser
#./mc admin user enable minio newuser
./mc admin user info minio newuser
./mc admin user remove minio newuser

# https://docs.min.io/docs/minio-admin-complete-guide.html
./mc admin policy list minio
./mc admin policy info minio readonly
./mc admin policy info minio writeonly
./mc admin policy info minio readwrite
cat > newbucketAdmin.json <<-'EOF'
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "s3:*"
    ],
    "Resource": [
      "arn:aws:s3:::newbucket/*"
    ]
  }]
}
EOF
./mc admin policy add minio newbucketAdmin newbucketAdmin.json
./mc admin policy remove minio newbucketAdmin

./mc admin policy set minio newbucketAdmin user=newuser
./mc admin policy unset minio newbucketAdmin user=newuser
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
#      - MINIO_SERVER_ACCESS_KEY=fa04fe9feb245ff2
#      - MINIO_SERVER_SECRET_KEY=63807858fa04fe9feb245ff21b581a9b
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
