# Python3

## 1 源码编译

```bash
cd /usr/local/src/
curl -o Python-3.8.14.tgz \
  https://www.python.org/ftp/python/3.8.14/Python-3.8.14.tgz
tar -zxvf Python-3.8.14.tgz
cd Python-3.8.14/
./configure --prefix=/usr/local/python38 --enable-optimizations
make -j2
make install

ln -s /usr/local/python38/bin/python3 /usr/bin/python3
python3 -V
python3 -m pip --version

ln -s /usr/local/python38/bin/pip3 /usr/bin/pip3
pip3 --version
```

## 2 配置 `pip` 国内镜像源

```bash
mkdir $HOME/.pip{,_cache}

# windows -> %USERPROFILE%/pip/pip.ini
mkdir -p $HOME/.pip/cache
cat > $HOME/.pip/pip.conf <<-EOF
[global]
cache-dir = $HOME/.pip/cache
index-url = http://mirrors.aliyun.com/pypi/simple

[install]
trusted-host = mirrors.aliyun.com
EOF
pip3 config list

# https://pypi.org/search/?q=pandas
pip3 install pandas==1.5.0
pip3 install -r requirements.txt
```
