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
server_url: http://yourserver.com:8080
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

# ns
headscale namespaces create default
headscale namespaces list
```

### 1.2 Docker

```bash
# download image
docker pull headscale/headscale:0.16.4

# config
tbd
```

## 2 Client

|No.|OS|Tailscale|Remark|
|:---:|:---:|:---:|-----|
|1|Linux|[`tailscale_1.30.2_amd64.tgz`](https://pkgs.tailscale.com/stable/tailscale_1.30.2_amd64.tgz)|*Official*|
|2|Android|[`tailscale-fdroid.apk`](https://github.com/FZR-forks/tailscale-android)|*Freedom*|
|3|Windows|[`tailscale-setup-1.30.2-amd64.msi`](https://pkgs.tailscale.com/stable/tailscale-setup-1.30.2-amd64.msi)|*Official*|
|4|MacOS|[`Tailscale-1.30.2-macos.zip`](https://pkgs.tailscale.com/stable/Tailscale-1.30.2-macos.zip)|*Official*|

## 2.1 Linux

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

# login
tailscale up --login-server=http://yourserver.com:8080 \
  --accept-routes=true --accept-dns=false
```
