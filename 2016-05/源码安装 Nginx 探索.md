# 源码安装 Nginx 探索



## 添加依赖

### PCRE

暂不支持 PCRE2，只能用8.x 版本。

```shell
wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.38.tar.gz -O pcre-8.38.tar.gz
tar -xvf pcre-8.38.tar.gz
```

### zlib

```shell
wget http://zlib.net/zlib-1.2.8.tar.gz -O zlib-1.2.8.tar.gz
tar -xvf zlib-1.2.8.tar.gz
```

### openssl

```shell
wget https://www.openssl.org/source/openssl-1.0.1t.tar.gz -O openssl-1.0.1t.tar.gz
tar -xvf openssl-1.0.1t.tar.gz
```

### ngx_devel_kit

```shell
wget https://github.com/simpl/ngx_devel_kit/archive/v0.3.0.tar.gz -O ngx_devel_kit-0.3.0.tar.gz
tar -xvf ngx_devel_kit-0.3.0.tar.gz
```

### lua-nginx-module

```shell
wget https://github.com/openresty/lua-nginx-module/archive/v0.10.5.tar.gz -O lua-nginx-module-0.10.5.tar.gz
tar -xvf lua-nginx-module-0.10.5.tar.gz 
```

### Lua

```shell
sudo apt-get install -y lua5.1 liblua5.1-0 liblua5.1-0-dev
sudo ln -s /usr/lib/x86_64-linux-gnu/liblua5.1.so /usr/lib/liblua.so
```

### LuaJIT

```shell
wget http://luajit.org/download/LuaJIT-2.0.4.tar.gz -O LuaJIT-2.0.4.tar.gz
tar -xvf LuaJIT-2.0.4.tar.gz
cd LuaJIT-2.0.4
make
sudo make install
```

## 安装 Nginx 源码

```shell
wget http://nginx.org/download/nginx-1.10.0.tar.gz?_ga=1.119827723.2067433858.1455285763 -O nginx-1.10.0.tar.gz
tar -xvf nginx.tar.gz
cd nginx-1.10.0.tar.gz

env LUAJIT_LIB=/usr/local/lib \
LUAJIT_INC=/usr/local/include/luajit-2.0 \
./configure \
--with-pcre=../pcre-8.38 \
--with-zlib=../zlib-1.2.8 \
--with-openssl=../openssl-1.0.1t \
--sbin-path=/usr/local/sbin \
--conf-path=/usr/local/nginx/nginx.conf \
--pid-path=/usr/local/nginx/nginx.pid \
--with-http_ssl_module \
--with-ld-opt="-Wl,-rpath,/usr/local/lib" \
--add-module=../ngx_devel_kit-0.3.0 \
--add-module=../lua-nginx-module-0.10.5

make
sudo make install
```
## Nginx 操作

## 启动

```shell
sudo /usr/local/sbin/nginx
```

### 结束

```shell
sudo kill -s QUIT ${NginxPid}
```
### 卸载

```shell
sudo rm -f -R /usr/local/nginx && sudo rm -f /usr/local/sbin/nginx
```