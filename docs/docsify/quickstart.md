# docsify建站指南

## 1 安装

```bash
# 安装
npm install -g docsify-cli --registry=https://registry.npm.taobao.org
```

## 2 初始化

```bash
# 初始化项目
docsify init ./docs
```

## 3 发布预览

```bash
# 本地预览
docsify serve docs
```

## 3 定时更新

```bash
# 定时任务
cat > gitpull.sh <<-EOF
#!/bin/bash
source /etc/profile
logfile=$HOME/crontab.log
date "+%F %T %Z" >> $logfile
cd $HOME/docsify-blog
git pull >> $logfile
EOF
chmod 755 gitpull.sh
crontab -e
'''
0 3 * * * $HOME/gitpull.sh
'''
crontab -l

# 执行日志
sudo tail -f /var/log/cron
```
