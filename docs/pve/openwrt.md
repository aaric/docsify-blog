# OpenWrt

> [OpenWrt 安装快速入门指南。](https://openwrt.org/zh/docs/guide-quick-start/start)

## 1 下载

&emsp;&emsp;*[阿里云开源镜像站 - OpenWrt。](https://mirrors.aliyun.com/openwrt/)*

## 2 创建虚拟机

### 2.1 基础配置

### 2.2 个性化配置

```bash
# 导入img文件并建立磁盘
gzip -d /var/lib/vz/template/iso/openwrt-21.02.3-x86-64-generic-squashfs-combined-efi.img.gz
qm importdisk 106 /var/lib/vz/template/iso/openwrt-21.02.3-x86-64-generic-squashfs-combined-efi.img local-lvm
```

### 2.2 网络配置

```bash
# 设置网络
vi /etc/config/network
'''
        option ipaddr '192.168.1.2'
        option gateway '192.168.1.1'
        option dns '114.114.114.114'
'''
# 重启网络（或reboot）
/etc/init.d/network restart

# 安装软件包
opkg update

## 1.中文支持
opkg install -y luci-i18n-base-zh-cn

## 2.主题
# https://github.com/jerrykuku/luci-theme-argon
# https://github.com/thinktip/luci-theme-neobird -- error: only for lede version
opkg install luci-theme-neobird_1.99-202201272020_all.ipk
opkg list_installed
opkg remove luci-theme-neobird

# 关机
poweroff
```
