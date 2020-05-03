Xtrabackup安装

cd /usr/local/mysql
wget获取xtrabackup

wget https://www.percona.com/downloads/XtraBackup/Percona-XtraBackup-2.2.12/binary/tarball/percona-xtrabackup-2.2.12-Linux-x86_64.tar.gz

解压
tar -zxvf percona-xtrabackup-2.2.12-Linux-x86_64.tar.gz

重命名
mv percona-xtrabackup-2.2.12-Linux-x86_64.tar.gz xtrabackup

复制服务到/usr/bin/
cd xtrabackup
cp innobackupex /usr/bin/
cp xtrabackup /usr/bin/

原始数据库整库备份：

./innobackupex —user=root —password=root —defaults-file=/etc/my.cnf /mnt/innobackup
其中，defaults-file是my.cnf的路径，/mnt/innobackup/为自建备份目录

scp 传输数据文件到目标服务器

目标服务器数据库恢复：
cd /usr/local/mysql
目标服务器mysql停用
service mysql stop

复制数据文件
innobackup —apply-log /usr/local/mysql/xtrabackup/xxxx-xxxxxxx
其中 /usr/local/mysql/xtrabackup/2015-xxxxxxx为备份数据所在位置

innobackup —copy-back —defaults-file=/etc/my.cnf /usr/local/mysql/xtrabackup/xxxx-xxxxxx
复制文件完成

更改data目录权限及所属
chown -R mysql:mysql data/

service mysql restart
启动服务，查看数据情况。