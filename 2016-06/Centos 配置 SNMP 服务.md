# Centos 配置 SNMP 服务

安装 NET-SNMP 组件

```shell
yum install -y net-snmp net-snmp-devel net-snmp-utils
```

停止 SNMP 服务以便配置用户

```shell
service snmpd stop
```

创建监控宝专用用户 *jiankongbao*

```shell
net-snmp-config --create-snmpv3-user -ro -A your_password -a MD5 jiankongbao
```

修改监听端口并启动 SNMP 服务

```shell
vim /etc/init.d/snmpd  # OPTIONS="-LS0-6d -Lf /dev/null -p /var/run/snmpd.pid udp:2161"
service snmpd start
```

设置开机启动

```shell
chkconfig snmpd on
```