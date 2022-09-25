# Python3

## 1 源码编译

```bash
# ModuleNotFoundError: No module named '_ctypes'
yum install libffi-devel -y

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
pip3 -V
```

## 2 配置 `pip` 国内镜像源

```bash
#mkdir -p $HOME/.pip/site-packages
mkdir -p $HOME/.pip/local
cat >> $HOME/.bash_profile <<-EOF

# python3
#export PYTHONPATH=$HOME/.pip/site-packages
export USERPYTHONBASE=$HOME/.pip/local
EOF
#python3 -m site --user-site
python3 -m site --user-base

# windows -> %USERPROFILE%/pip/pip.ini
mkdir -p $HOME/.pip/cache
cat > $HOME/.pip/pip.conf <<-EOF
[global]
cache-dir = $HOME/.pip/cache
#index-url = http://mirrors.aliyun.com/pypi/simple
index-url = https://pypi.tuna.tsinghua.edu.cn/simple

[install]
#trusted-host = mirrors.aliyun.com
trusted-host = pypi.tuna.tsinghua.edu.cn
EOF
pip3 config list

# https://pypi.org/search/?q=numpy
pip3 install numpy==1.21.6
pip3 show numpy
pip3 install -r requirements.txt
ls $(pip3 cache dir)
```
