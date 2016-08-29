# Python2 和 Python3 版本共存初始化

## 初始化 Python2

```shell
sudo apt-get install -y python-dev python-pip
sudo cp /usr/bin/pip /usr/local/bin/pip2
```

## 初始化 Python3

因 apt 源上的 Python3 版本是 3.2，所以使用源码安装3.5.2。

```shell
sudo apt-get install -y build-essential libsqlite3-dev sqlite3 bzip2 libbz2-dev

wget https://www.python.org/ftp/python/3.5.2/Python-3.5.2.tgz
tar -xvf Python-3.5.2.tgz
cd Python-3.5.2
./configure --prefix=/usr/
make
sudo make install
sudo cp /usr/bin/pip* /usr/local/bin/
```

## 安装依赖

### 声明通用依赖

```shell
common_packages='
PyYAML
wheel
gunicorn
celery
ipython
pep8
pylint
pytest
redis
wrapt
jsonschema
requests
tornado
Django
Flask
six
'

# 为了安装 mysql 相关库
sudo apt-get install -y libmysqlclient-dev
```

### 为 Python2 安装依赖

```shell
sudo pip2 install -q \
fabric \
supervisor \
MySQL-python \
pathlib2 \
virtualenv \
backports.shutil_get_terminal_size \
${common_packages}
```

### 为 Python3 安装依赖

```shell
sudo pip3 install -q \
mysqlclient \
${common_packages}
```


## 命令版本测试

根据以上方式初始化 Python 环境后，可以做到 Python2 和 Python3 共存，但是默认命令依旧使用 Python2 以保证兼容性，同时额外提供了指定确切版本的命令别名。

```shell
python --version  # Python 2.7.3
python2 --version  # Python 2.7.3
python3 --version  # Python 3.5.2
pip --version  # pip 1.0 from /usr/lib/python2.7/dist-packages (python 2.7)
pip2 --version  # pip 1.0 from /usr/lib/python2.7/dist-packages (python 2.7)
pip3 --version  # pip 8.1.1 from /usr/lib/python3.5/site-packages (python 3.5)
```



## 创建可供 www-data 使用的独立干净的 Python 环境

此环境为后续 Python 自动部署准备

```shell
sudo virtualenv /opt/python2
sudo pyvenv /opt/python3

sudo chown -R www-data:www-data /opt/python2
sudo chown -R www-data:www-data /opt/python3
```


在该环境下安装依赖不需要 root 权限，且不会影响到系统的 Python 环境

```shell
# 激活 Python2
source /opt/python2/bin/activate
python --version  # Python 2.7.3
# 退出环境
deactivate

# 激活 Python3
source /opt/python3/bin/activate
python --version  # Python 3.5.2
# 退出环境
deactivate
```

