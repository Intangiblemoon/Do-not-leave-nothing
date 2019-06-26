#mysql安装
##前置准备
- 卸载系统自带的Mariadb
[root@hdp265dnsnfs ~]# rpm -qa|grep mariadb mariadb-libs-5.5.44-2.el7.centos.x86_64
[root@hdp265dnsnfs ~]# rpm -e --nodeps mariadb-libs-5.5.44-2.el7.centos.x86_64

- 删除etc目录下的my.cnf文件
[root@hdp265dnsnfs ~]# rm /etc/my.cnf
rm: cannot remove ?etc/my.cnf? No such file or directory #检查mysql是否存在
[root@hdp265dnsnfs ~]# rpm -qa | grep mysql

- 创建一个用户名为mysql的用户并加入mysql用户组
[root@hdp265dnsnfs ~]# useradd -s /sbin/nologin mysql

- 上传文件包
cd /usr/local/
tar -xvzf /root/mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz
mv mysql-5.7.22-linux-glibc2.12-x86_64 mysql

- 更改所属的组和用户
chown -R mysql:mysql mysql

##安装 

- 添加配置
vi /etc/my.cnf
```
[mysql]
default-character-set=utf8
[mysqld]
skip-name-resolve
port = 3306
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
max_connections=1000
character-set-server=utf8
default-storage-engine=INNODB
lower_case_table_names=1
max_allowed_packet=100M
```

- 初始化
cd mysql
bin/mysql_install_db --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/
cp ./support-files/mysql.server /etc/init.d/mysqld
chmod 755 /etc/my.cnf
chmod +x /etc/init.d/mysqld

##mysql数据库配置
- 自启动 可跳过
chkconfig --level 35 mysqld on
chkconfig --list mysqld
chmod +x /etc/rc.d/init.d/mysqld
chkconfig --add mysqld
chkconfig --list mysqld

- 启动方法
service mysqld status
systemctl restart mysqld

- 添加软连接
ln -s /usr/local/mysql/bin/mysql /usr/bin

- 添加环境变量
vi /etc/profile
export PATH=$PATH:/use/local/mysql/bin 
source /etc/profile

- 获得初始密码
cat /root/.mysql_secret 


- 修改密码
mysql -uroot -p
mysql> set PASSWORD = PASSWORD('111111');
mysql> flush privileges; 

- 添加远程访问权限
mysql> use mysql 
mysql> update user set host='%' where user='root';
create user 'xxx'@'%' identified by '123'; 这里 @‘%’ 表示在任何主机都可以登录

- 重启生效
systemctl restart mysqld

为了安全一般不给root远程访问权限 可以再创建一个别的用户，并给他远程访问权限
grant all privileges on *.* to user@'%' identified by 'user';
flush privileges;