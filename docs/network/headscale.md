# Headscale

> *Headscale 由欧洲航天局的 Juan Font 使用 Go 语言开发，在 BSD 许可下发布，实现了 Tailscale 控制服务器的所有主要功能，可以部署在企业内部，没有任何设备数量的限制，且所有的网络流量都由自己控制。*

## 1 Server

### 1.1 Binary

```bash
# download
curl -o /usr/local/bin/headscale \
  https://github.com/juanfont/headscale/releases/download/v0.16.4/headscale_0.16.4_linux_amd64
chmod 755 /usr/local/bin/headscale

# config
mkdir /etc/headscale
curl -o /etc/headscale/config.yaml \
  https://github.com/juanfont/headscale/raw/v0.16.4/config-example.yaml
vim /etc/headscale/config.yaml
'''
server_url: http://yourserver.com:38080
listen_addr: 0.0.0.0:38080
ip_prefixes:
  #- fd7a:115c:a1e0::/48
  - 100.64.0.0/10
'''

# data
mkdir /var/lib/headscale
touch /var/lib/headscale/db.sqlite

# systemd
cat > /etc/systemd/system/headscale.service <<-'EOF'
[Unit]
Description=headscale - https://github.com/juanfont/headscale/wiki/nginx-configuration
Documentation=https://github.com/juanfont/headscale
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/headscale serve
ExecStop=/bin/kill -s QUIT $MAINPID
Restart=always
RestartSec=5

# Optional security enhancements
NoNewPrivileges=yes
PrivateTmp=yes
ProtectSystem=strict
ProtectHome=yes
ReadWritePaths=/var/lib/headscale /var/run/headscale
AmbientCapabilities=CAP_NET_BIND_SERVICE
RuntimeDirectory=headscale

[Install]
WantedBy=multi-user.target
EOF
systemctl daemon-reload
systemctl enable headscale --now
systemctl status headscale

# test
ss -tulnp | grep headscale

# namespaces
headscale namespaces create default
headscale namespaces list

# nodes
headscale -n default nodes register \
  --key e817be4b3dfe8ea5c73fe0b68f0bbd497c9b9d0430c869668c4872308e949c63
headscale nodes list

# routes | --advertise-routes
headscale routes enable -i 1 -r "192.168.100.0/24"
headscale routes list -i 1
```

### 1.2 Docker

```bash
# download image
docker pull headscale/headscale:0.16.4

# config
curl -o config/config.yaml \
  https://github.com/juanfont/headscale/raw/v0.16.4/config-example.yaml
vim headscale_config/config.yaml
'''
server_url: http://yourserver.com:38080
listen_addr: 0.0.0.0:38080
ip_prefixes:
  #- fd7a:115c:a1e0::/48
  - 100.64.0.0/10
'''

# deploy
cat > docker-compose.yaml <<-'EOF'
version: "3"
services:
  derper:
    image: ik8share/ip_derper:1.22.2
    container_name: derper_community_edition
    network_mode: "host"
    environment:
      DERP_ADDR: ":30443"
      DERP_HTTP_PORT: 30080
    ports:
      - 30443:30443
      - 3478:3478/udp
    restart: always
    deploy:
      restart_policy:
        condition: on-failure
  headscale:
    image: ik8share/headscale:0.16.4
    container_name: headscale_community_edition
    cap_add:
      - CAP_NET_BIND_SERVICE
    volumes:
      - ./headscale_config:/etc/headscale/
      - ./headscale_data:/var/lib/headscale
    ports:
      - 38080:38080
      - 41641:41641
    command: headscale serve
    restart: unless-stopped
    deploy:
      restart_policy:
        condition: on-failure
EOF
docker-compose --compatibility up -d

# namespaces
docker exec headscale_community_edition headscale namespaces create default
docker exec headscale_community_edition headscale namespaces list

# nodes
docker exec headscale_community_edition headscale -n default nodes register --key e817be4b3dfe8ea5c73fe0b68f0bbd497c9b9d0430c869668c4872308e949c63
docker exec headscale_community_edition headscale nodes list

# routes | --advertise-routes
docker exec headscale_community_edition headscale routes enable -i 1 -r "192.168.100.0/24"
docker exec headscale_community_edition headscale routes list -i 1
```

## 2 Client

|No.|OS|Tailscale|Remark|
|:---:|:---:|:---:|-----|
|1|Linux|[`tailscale_1.30.2_amd64.tgz`](https://pkgs.tailscale.com/stable/tailscale_1.30.2_amd64.tgz)|*Official*|
|2|Android|[`tailscale-fdroid.apk`](https://github.com/FZR-forks/tailscale-android)|*Freedom*|
|3|Windows|[`tailscale-setup-1.30.2-amd64.msi`](https://pkgs.tailscale.com/stable/tailscale-setup-1.30.2-amd64.msi)|*Official*|
|4|MacOS|[`Tailscale-1.30.2-macos.zip`](https://pkgs.tailscale.com/stable/Tailscale-1.30.2-macos.zip)|*Official*|

### 2.1 Linux

> [Setting up Tailscale on CentOS 7](https://tailscale.com/kb/1048/install-centos-7/)

```bash
# download
curl -o tailscale_1.30.2_amd64.tgz \
  https://pkgs.tailscale.com/stable/tailscale_1.30.2_amd64.tgz

# binary
tar -zxvf tailscale_1.30.2_amd64.tgz
cp tailscale_1.30.2_amd64/tailscaled /usr/sbin/tailscaled
cp tailscale_1.30.2_amd64/tailscale /usr/bin/tailscale

# systemd
cp tailscale_1.30.2_amd64/systemd/tailscaled.defaults /etc/default/tailscaled
cp tailscale_1.30.2_amd64/systemd/tailscaled.service /lib/systemd/system/tailscaled.service
systemctl daemon-reload
systemctl enable tailscaled --now
systemctl status tailscaled

# login - server confirm
tailscale up --login-server=http://yourserver.com:38080 \
  --accept-routes=true --accept-dns=false

# login | routes - server confirm
cat > /etc/sysctl.d/tailscale.conf <<-'EOF'
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
EOF
sysctl -p /etc/sysctl.d/tailscale.conf
tailscale up --login-server=http://yourserver.com:38080 \
  --accept-routes=true --accept-dns=false \
  --advertise-routes=192.168.100.0/24
```

### 2.2 Windows

```powershell
# download | http://yourserver.com:38080/windows
curl -o tailscale.reg http://yourserver.com:38080/windows/tailscale.reg
regedit /s tailscale.reg

# or
REG ADD "HKLM\Software\Tailscale IPN" /v UnattendedMode /t REG_SZ /d always
REG ADD "HKLM\Software\Tailscale IPN" /v LoginURL /t REG_SZ /d "http://yourserver.com:38080"
```
