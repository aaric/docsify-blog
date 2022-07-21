# Proxmox VE

## 1 准备

### 1.1 VirsulBox设置

&emsp;&emsp;启用 `PAE/NX` 和 `嵌套VT-x/AMD-V` 设置。其中，`嵌套VT-x/AMD-V` 需要执行以下代码：

```powershell
.\VBoxManage.exe list vms
.\VBoxManage.exe modifyvm "PVE01" --nested-hw-virt on
```

![pve-vbox-cpu.png](../img/pve-vbox-cpu.png ":size=75%")

&emsp;&emsp;设置网卡连接方式为`桥接网卡`，混杂模式： `全部允许`。

![pve-vbox-network.png](../img/pve-vbox-network.png ":size=75%")

### 1.2 Rufus启动盘

&emsp;&emsp;设置分区为 `MBR` 。

![pve-rufus-s1.png](../img/pve-rufus-s1.png ":size=400")

&emsp;&emsp;选择以 `DD` 镜像模式写入。

![pve-rufus-s2.png](../img/pve-rufus-s2.png ":size=500")

## 2 安装步骤

&emsp;&emsp;待完善。。。

## 3 附录

### 3.1 管理平台：无法访问

```bash
# version
cat /etc/debian_version

# https://ip:8006
lsof -i:8006
systemctl status pveproxy
systemctl status pvedaemon
systemctl status pvestatd

# restart
systemctl restart pveproxy
systemctl restart pvedaemon
systemctl restart pvestatd
```

### 3.2 提示：无效订阅

```bash
# modify
vi /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js
'''
                    if (res === null || res === undefined || !res || res
                        .data.status.toLowerCase() !== 'active') {
                        /*Ext.Msg.show({
                            title: gettext('No valid subscription'),
                            icon: Ext.Msg.WARNING,
                            message: Proxmox.Utils.getNoSubKeyHtml(res.data.url),
                            buttons: Ext.Msg.OK,
                            callback: function(btn) {
                                if (btn !== 'ok') {
                                    return;
                                }
                                orig_cmd();
                            },
                        });*/
'''

# retart
systemctl restart pveproxy
```

### 3.3 切换阿里云Debian源

```bash
vi /etc/apt/sources.list
'''
deb http://ftp.debian.org/debian bullseye main contrib

deb http://ftp.debian.org/debian bullseye-updates main contrib

# security updates
deb http://security.debian.org bullseye-security main contrib
'''

mv /etc/apt/sources.list /etc/apt/sources.list.bak220721

cat > /etc/apt/sources.list <<-'EOF'
deb http://mirrors.aliyun.com/debian/ bullseye main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye main non-free contrib
deb http://mirrors.aliyun.com/debian-security/ bullseye-security main
deb-src http://mirrors.aliyun.com/debian-security/ bullseye-security main
deb http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
deb http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
EOF

apt update
```

### 3.4 切换PVE企业版源为CEPH源

```bash
vi /etc/apt/sources.list.d/pve-enterprise.list
'''
#deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise
'''

cat > /etc/apt/sources.list.d/pve-ceph.list <<-'EOF'
deb http://mirrors.ustc.edu.cn/proxmox/debian/ceph-nautilus buster main
EOF

apt update

apt install ifupdown2
```
