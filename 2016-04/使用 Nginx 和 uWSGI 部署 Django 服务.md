# 使用 Nginx 和 uWSGI 部署 Django 服务
## Django 部分
### 配置

- *SECRET_KEY*: Django 中涉及到安全和加密的地方都用到这个值(csrf, 加密等),因此不可泄漏.
- *ALLOWED_HOSTS*: 关闭 Debug 开关后如没有设置这个值会导致不能访问.

### 初始化

- 收集静态文件: `python manage.py collectstatic`
- 创建迁移文件: `python manage.py makemigrations`
- 应用迁移文件: `python manage.py migrate`
- 创建本地化语言文件: `python manage.py makemessages`

## uWSGI 部分
### uWSGI 配置

```ini
[uwsgi]
# 环境相关设置
env = PYTHONPATH=/path/to/project
env = DJANGO_SETTINGS_MODULE=your_project.settings

# 项目相关设置
chdir = /path/to/project
wsgi-file = /path/to/project/wsgi.py
uid = nobody
gid = nogroup

# uWSGI
master = True
vacuum = True # 关闭时回收所有打开的文件和 socket
socket = 127.0.0.1:8080
processes = 8 # worker 个数
threads = 4 # 每个 worker 线程数
pidfile = /tmp/your_project.pid
log-date=%%a, %%d %%b %%Y %%T %%z # 两个%是必须
logto=/path/to/log/uwsgi_logto.log

# 安全设置
max-requests = 5000 # 一个 worker 处理了指定的请求个数后会被重启
reload-on-as = 256 # 使用的虚拟内存超过指定的值(MB)会被重启
reload-on-rss = 128 # 使用的实际内存超过指定的值(MB)会被重启
```

### uWSGI params

```text
uwsgi_param  QUERY_STRING       $query_string;
uwsgi_param  REQUEST_METHOD     $request_method;
uwsgi_param  CONTENT_TYPE       $content_type;
uwsgi_param  CONTENT_LENGTH     $content_length;

uwsgi_param  REQUEST_URI        $request_uri;
uwsgi_param  PATH_INFO          $document_uri;
uwsgi_param  DOCUMENT_ROOT      $document_root;
uwsgi_param  SERVER_PROTOCOL    $server_protocol;
uwsgi_param  HTTPS              $https if_not_empty;

uwsgi_param  REMOTE_ADDR        $remote_addr;
uwsgi_param  REMOTE_PORT        $remote_port;
uwsgi_param  SERVER_PORT        $server_port;
uwsgi_param  SERVER_NAME        $server_name;
```

## Nginx 部分

```text
upstream myapp {
    server 127.0.0.1:8080; # 可以设置多个 upstream 作负载均衡
}

server {
    listen 80; # Nginx 监听端口
    server_name www.example.com; # 转发来自 www.example.com 的请求
    charset utf-8;

    ssi on;

	# gzip 设置
    gzip on;
    gzip_disable "msie6";
    gzip_types text/plain application/xml text/xml application/x-rsication/javascript application/x-javascript application/json;
    gzip_comp_level 9;
    gzip_min_length 1400;
    gzip_vary on;

    client_max_body_size 50M; # 最大上传文件大小

    location /static {
        alias /path/to/your/project/static;
    }

    location / {
        uwsgi_pass myapp; # upstream
        include /path/to/project/uwsgi_params;
    }
}
```
