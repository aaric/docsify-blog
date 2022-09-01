# NPS

> [一款轻量级、高性能、功能强大的内网穿透代理服务器](https://ehang-io.github.io/nps/)

## 1 Sever

```bash
# download
curl -o linux_amd64_server-v0.26.10.tar.gz \
  https://github.com/ehang-io/nps/releases/download/v0.26.10/linux_amd64_server.tar.gz
mkdir /opt/nps-0.26.10 \
  && tar -zxvf linux_amd64_server-v0.26.10.tar.gz -C /opt/nps-0.26.10/

# install
cd /opt/nps-0.26.10/
vim conf/nps.conf
'''
#Boot mode(dev|pro)
runmode=dev

#HTTP(S) proxy port, no startup if empty
http_proxy_port=8000
https_proxy_port=44300
https_just_proxy=false

##bridge
bridge_type=tcp
bridge_port=8024

#web
web_host=nps.yesoo.top
web_username=admin
web_password=admin123
web_base_url=/nps

# Public password, which clients can use to connect to the server
public_vkey=123
'''
./nps install

# startup
# http://localhost:8080 | admin | 123
cd
nps start
tail -f /var/log/nps.log
```

## 2 Client

```bash
# download
curl -o linux_amd64_client-v0.26.10.tar.gz \
  https://github.com/ehang-io/nps/releases/download/v0.26.10/linux_amd64_client.tar.gz
mkdir /opt/npc-0.26.10 \
  && tar -zxvf linux_amd64_client-v0.26.10.tar.gz -C /opt/npc-0.26.10/

# server
# create new client

# client
./npc -server 127.0.0.1:8024 -vkey=123
```
