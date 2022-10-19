# Python3

## 1 [Python](https://www.python.org/downloads/)

### 1.1 make

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

### 1.2 pip.conf

```bash
#mkdir -p $HOME/.pip/site-packages
mkdir -p $HOME/.pip/local
cat >> $HOME/.bash_profile <<-EOF

# python3
#export PYTHONPATH=$HOME/.pip/site-packages
export PYTHONUSERBASE=$HOME/.pip/local
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
pip3 config list -v

# https://pypi.org/search/?q=numpy
pip3 install numpy==1.21.6
pip3 show numpy
pip3 install -r requirements.txt
ls $(pip3 cache dir)
```

## 2 [Miniconda3](https://docs.conda.io/en/main/miniconda.html)

### 2.1 .condarc

```powershell
conda info
notepad C:\Users\admin\.condarc
'''
# https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
envs_dirs:
  - E:\cache_python\conda\envs
  - C:\Users\admin\.conda\envs
  - C:\ProgramData\Miniconda3\envs
  - C:\Users\admin\AppData\Local\conda\conda\envs
pkgs_dirs:
  - E:\cache_python\conda\pkgs
  - C:\ProgramData\Miniconda3\pkgs
  - C:\Users\admin\.conda\pkgs
  - C:\Users\admin\AppData\Local\conda\conda\pkgs
'''
conda clean -i
```

### 2.2 pip.ini

```powershell
pip config list -v
mkdir C:\Users\admin\pip

# extra: https://pypi.doubanio.com/simple
notepad C:\Users\admin\pip\pip.ini
'''
[global]
cache-dir = E:\cache_python\conda\pip
index-url = https://pypi.tuna.tsinghua.edu.cn/simple

[install]
trusted-host = pypi.tuna.tsinghua.edu.cn
'''
pip config get global.cache-dir
```
