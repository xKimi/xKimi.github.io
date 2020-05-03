Percona Server安装配置
1.wget下载Percona Server源码包

2.安装依赖libs

yum install -y gcc gcc-c++ autoconf automake zlib fiex libxml ncurses-devel libmcrypt libtool-ltdl-devel* cmake bison
yum install dtrace systemtap-sdt-devel -y
yum install bison-devel bison -y

3.解压缩，配置make参数
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/usr/local/mysql/data -DSYSCONFDIR=/etc -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_READLINE=1 -DMYSQL_UNIX_ADDR=/var/run/mysqld.sock -DMYSQL_TCP_PORT=3306 -DENABLED_LOCAL_INFILE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DEXTRA_CHARSETS=all -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_general_ci

4.安装 make && make install
如果编译失败，清理源码目录下的 CMakeCache.txt 文件，重新编译即可。

5.添加mysql用户
groupadd mysql
useradd mysql -g mysql
chown -R mysql: mysql / usr/local /mysql
chown -R mysql: mysql /data/mysql
chown –R mysql:mysql /var/lib/mysql/mysql.socket

6.添加服务
cp support-files/mysql.server /etc/init.d/mysql

7.配置 my.cnf
方法： 修改/etc/my.conf:
[mysqld]
datadir=/usr/local/mysql/data
socket=/var/lib/mysql/mysql.sock

[mysql.server]
user=mysql
basedir=/usr/local/mysql

[client]
socket=/var/lib/mysql/mysql.sock

8.初始化数据库
./scripts/mysql_install_db –user=mysql

9.启动MySQL服务

service mysql start
进入数据库
mysql -u root -p
use mysql;
添加admin用户
insert into mysql.user(Host,User,Password) values(“%”,”admin”,password(“your_admin_password”));

修改root用户密码
update user set password=PASSWORD(‘new_password’) where user=’root’;

删除多余用户
delete from mysql.user where host=’’;
delete from mysql.user where host=’127.0.0.1’;
delete from mysql.user where host=’localhost’;
delete from mysql.user where host=’::1’;

select user,host,password from mysql.user;

授权可以在其他机器以admin访问数据库
grant all privileges on . to admin@% identified by ‘your_password’;
flush privileges;