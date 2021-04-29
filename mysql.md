# mysql的安装

#### 1.yum安装

##### 1.查看是否安装了mysql

```
[root@localhost ~]# rpm -qa|grep mysql #无输出说明没有安装
```

##### 2.下载对应的yum包

打开https://dev.mysql.com/downloads/repo/yum/   [MySQL Yum存储库](https://dev.mysql.com/downloads/repo/yum/) 

查看自己centos对应的版本 

```
cat /etc/redhat-release 
```

下载自己对应版本的安装包和安装mysql源

```
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
rpm -Uvh mysql80-community-release-el7-3.noarch.rpm
```

如果显示wget未找到命令，则需要先安装wget：

yum -y install wget



 检查是否安装成功：
执行成功后会在/etc/yum.repos.d/目录下生成两个repo文件mysql-community.repo及 mysql-community-source.repo 



##### 3.查看mysql相关资源

```
yum repolist enabled | grep "mysql.*-community.*" 
```

选择masql版本
使用MySQL Yum Repository安装MySQL，默认会选择当前最新的稳定版本

- 使用命令：yum repolist all | grep mysql，查看当前yum repolist的所有版本
- 切换版本

```
[root@localhost ~] sudo yum-config-manager --disable mysql80-community
[root@localhost ~] sudo yum-config-manager --enable mysql57-community
```



##### 4.安装mysql

```
[root@localhost ~]# sudo yum install mysql-community-server
```

 该命令会安装MySQL服务器 (mysql-community-server) 及其所需的依赖、相关组件，包括mysql-community-client、mysql-community-common、mysql-community-libs等。安装时间会比较长 



##### 5.启动

```
[root@localhost ~]# systemctl start mysqld.service
[root@localhost ~]# systemctl status mysqld.service
```

##### 6.停止

```
systemctl stop mysqld.service
```

##### 7.重启

```
[root@localhost ~]# systemctl restart mysqld.service
```



##### 8.设置编码

查看编码

```
SHOW VARIABLES LIKE 'character%';
```

设置编码
编辑/etc/my.cnf，[mysqld]节点增加以下代码

```
[mysqld]
character_set_server=utf8
init-connect='SET NAMES utf8'
```

##### 9.设置开机启动

```
[root@localhost ~]# systemctl enable mysqld
[root@localhost ~]# systemctl daemon-reload
```





##### 10.关闭防火墙

允许远程登录

```
# firewall防火墙
systemctl status firewalld
# 查看转态
firewall-cmd --state
# 开启
service firewalld start
# 重启
service firewalld restart
# 关闭
service firewalld stop
#注意分清楚linux的版本 命令会有所不同
#查看防火墙规则
firewall-cmd --list-all 
```



##### 11.删除mysql

1. 检查mariadb

```
[root@localhost ~]# rpm -qa|grep mariadb
```

如无则跳过下面一条

​	2.删除mariadb

```
[root@localhost ~]# rpm -e --nodeps mariadb-server
[root@localhost ~]# rpm -e --nodeps mariadb
[root@localhost ~]# rpm -e --nodeps mariadb-libs
```

​	3.检查mysql

```
[root@localhost ~]# rpm -qa|grep mysql
mysql-community-client-8.0.22-1.el7.x86_64
mysql-community-common-8.0.22-1.el7.x86_64
mysql-community-client-plugins-8.0.22-1.el7.x86_64
mysql-community-libs-compat-8.0.22-1.el7.x86_64
mysql80-community-release-el7-3.noarch
mysql-community-libs-8.0.22-1.el7.x86_64
mysql-community-server-8.0.22-1.el7.x86_64
```



4.删除mysql

```
[root@localhost ~]# rpm -e --nodeps xxx
```



##### 12.参考地址

https://www.baidu.com/link?url=Ne6CTsgudJ7t_1zMasWtcUHsnI5o7aV8aJPaWyo2FC7mMFtkm6SgUIXdJZ4omkff7g80TY0MV_GccAJXnRqyfSxl4b8zCgN8_XmDhGZ-qKe&wd=&eqid=fd47127f0008d108000000056080dcfc





# mysql的用户管理

##### 1.修改密码及登录

**初始密码**
MySQL第一次启动后会创建超级管理员账号root@localhost，初始密码存储在日志文件中：

```
[root@localhost ~]# grep 'temporary password' /var/log/mysqld.log
2020-12-06T07:32:41.399112Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: Nd<-p!Fxt6A8
```



修改默认密码

```
# 先进行登录
[root@localhost ~]# mysql -uroot -p
Enter password: 
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements 
#密码太弱
ALTER USER 'root'@'localhost' IDENTIFIED BY '!@#QWEasd123'
```

解决方法如下：

使用复杂密码，MySQL默认的密码策略是要包含数字、字母及特殊字符；
如果只是测试用，不想用那么复杂的密码，可以修改默认策略，即validate_password_policy（以及validate_password_length等相关参数），使其支持简单密码的设定，具体方法可以参考：设置密码策略；
修改配置文件/etc/my.cnf，添加validate_password=OFF，保存并重启MySQL
修改报错后重启报错解决可参考：https://blog.csdn.net/aiyowei1106/article/details/88703746



##### 2.远程连接数据库的问题解决

https://www.cnblogs.com/xujingyang/p/9479811.html

```
设置成所有用户能访问
update user set host = '%' where user = 'root';
```



##### 3.修改mysql8密码加密方式

 

```
use mysql;
ALTER USER 'backup_user'@'localhost' IDENTIFIED BY '!@#QWEasd123' PASSWORD EXPIRE NEVER;


```



# mysql的主从复制架构搭建

##### 1.在主库上创建一个用于主从复制的账号:

```
create user 'backup_user'@'192.168.199.148%' identified by '!@#QWEasd123';
grant replication slave on *.* to 'backup_user'@'192.168.199.148%';
flush privileges;
```

