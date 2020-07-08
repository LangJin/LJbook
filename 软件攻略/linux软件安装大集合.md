
# linux上安装java的步骤
1、通过filezilla这个工具，连接上Linux服务器，然后将我们准备好的Java和tomcat的安装包传输到服务器的/root路径下。
2、对jdk进行解压
```sh
tar zxvf /root/jdk-8u211-linux-x64.tar.gz
ll
```
3、在根目录的usr这个文件夹里面创建一个叫java的文件夹。
```sh
mkdir /usr/java
```
4、将我们解压后出现的那个文件夹jdk1.8.0_211移动到上一步创建的java文件夹中。
```sh
mv /root/jdk1.8.0_211 /usr/java
```
5、使用vi命令 编辑根目录下的etc中的profile这个文件。
```sh
vi /etc/profile
```
6、将这段内容写到profile这个文件里面的done的下面一行，然后保存。
```sh
export JAVA_HOME=/usr/java/jdk1.8.0_211
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```
7、加载profile文件，使其修改生效。
```
source /etc/profile
```
8、检查java是否安装成功。
```sh
java -version
javac -version
```



# tomcat的安装步骤
1、通过filezilla这个工具，连接上Linux服务器，然后将我们准备好的Java和tomcat的安装包传输到服务器的/root路径下。
2、对tomcat压缩包进行解压。 
```sh
tar zxvf /root/apache-tomcat-8.5.43.tar.gz
ll
```
3、在根目录的usr这个文件夹里面创建一个叫tomcat的文件夹。
```sh
mkdir /usr/tomcat
```
4、将我们解压后出现的那个文件夹移动到上一步创建的tomcat文件夹中。
```sh
mv /root/apache-tomcat-8.5.43 /usr/tomcat
```
5、对tomcat进行配置，对setclasspath.sh这个文件进行编辑。
```sh
vi /usr/tomcat/apache-tomcat-8.5.43/bin/setclasspath.sh
```
6、在setclasspath.sh这个文件的第二排写入以下内容：
```sh
export JAVA_HOME=/usr/java/jdk1.8.0_211
export JAVA_JRE=/usr/java/jdk1.8.0_211/bin
```
7、启动tomcat。
```sh
sh /usr/tomcat/apache-tomcat-8.5.43/bin/startup.sh
```
8、在阿里云中，打开端口号。（一般这个步骤可以不做，除非你的服务器的8080端口没有打开。）
9、在浏览器中访问自己的tomcat的页面。


# MySQL的离线rpm包安装步骤
1、通过filezilla这个工具，连接上Linux服务器，然后将我们准备好的mysql的安装包传输到服务器的/root路径下。
2、对mysql压缩包进行解压。
```sh
mkdir /root/mysql
mv /root/mysql-5.7.26-1.el7.x86_64.rpm-bundle.tar /root/mysql
tar xf /root/mysql/mysql-5.7.26-1.el7.x86_64.rpm-bundle.tar
cd /root/mysql
ll
```
3、安装numactl（必要组件，不安装会导致后面的步骤出现依赖的问题。）
```sh
yum -y install numactl
```
3.1、卸载mariadb（这是系统自带的数据库，不卸载会导致MySQL安装失败。）
```sh
rpm -qa | grep -i mariadb
rpm -e --nodeps mariadb-libs-5.5.60-1.el7_5.x86_64  # 这个文件名字是上一步查出来的
```
3.2、安装mysql，按顺序安装下面4个rpm（版本号可能不一样）。
```sh
rpm -ivh mysql-community-common-5.7.26-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.26-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-5.7.26-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-5.7.26-1.el7.x86_64.rpm
```
4、启动数据库
```sh
systemctl start  mysqld.service  # 启动数据库
systemctl status mysqld.service  # 检查数据库运行状态
systemctl stop mysqld.service  # 停止数据库
```
6、数据库安装成功后，先生成一个随机密码，获取密码。
```sh
cat /var/log/mysqld.log | grep password
```
7、使用上一步获取的密码连接数据库.
```sh
mysql -u root -p  # 回车后输入密码
```
8、进入数据库后，必须修改密码重新连接后才能做其他的操作，所以修改密码为1qaz!QAZ，命令：
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '1qaz!QAZ';
exit;
```
9、退出数据库，用新密码重新登录。 
```sh
mysql -u root -p  # 回车后输入密码1qaz!QAZ
```
10、创建一个具有远程访问权限的账号。
```sql
create user 'root'@'%' identified with mysql_native_password by '1qaz!QAZ';  
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;
```
10.1、为了让数据库的密码能修改为123456，所以我们需要对数据库进行一些配置。
```sql
SHOW VARIABLES LIKE 'validate_password%';   # 查看数据库的密码规则
set global validate_password_policy=LOW;   # 修改密码强度要求
set global validate_password_length=6;   # 修改密码长度要求
```
11、好了数据库的设置结束了，你现在可以尝试能不能用navicat来连接了。
12、如果不能，那八成是端口的问题。所以检查阿里云的控制台的安全组是否开放端口。
13、通过命令查看当前已经开放的端口。
```sh
netstat -ntlp
```
14、如果不存在3306，那么通过以下2个命令打开3306端口号。
将3306端口添加到防火墙例外并重启。
```sh
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload
```
15、再次尝试navicat能连接了不。


# MySQL的在线安装步骤
1、下载mysql官方的yum工具。备注：yum可以理解成是一个应用市场。
```sh
cd /root
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```
2、用yum安装MySQL的基础配置。
```sh
yum -y install /root/mysql57-community-release-el7-10.noarch.rpm
```
3、用yum下载并安装mysql的核心服务。
```sh
yum -y install mysql-community-server
```
4、启动数据库
```sh
systemctl start  mysqld.service  # 启动数据库
systemctl status mysqld.service  # 检查数据库运行状态
systemctl stop mysqld.service  # 停止数据库
```
6、数据库安装成功后，先生成一个随机密码，获取密码。
```sh
cat /var/log/mysqld.log | grep password
```
7、使用上一步获取的密码连接数据库.
```sh
mysql -u root -p  # 回车后输入密码
```
8、进入数据库后，必须修改密码重新连接后才能做其他的操作，所以修改密码为1qaz!QAZ，命令：
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '1qaz!QAZ';
exit;
```
9、退出数据库，用新密码重新登录。 
```sh
mysql -u root -p  # 回车后输入密码1qaz!QAZ
```
10、创建一个具有远程访问权限的账号。
```sql
create user 'root'@'%' identified with mysql_native_password by '1qaz!QAZ';  
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;
```
10.1、为了让数据库的密码能修改为123456，所以我们需要对数据库进行一些配置。
```sql
SHOW VARIABLES LIKE 'validate_password%';   # 查看数据库的密码规则
set global validate_password_policy=LOW;   # 修改密码强度要求
set global validate_password_length=6;   # 修改密码长度要求
```
11、好了数据库的设置结束了，你现在可以尝试能不能用navicat来连接了。
12、如果不能，那八成是端口的问题。所以检查阿里云的控制台的安全组是否开放端口。
13、通过命令查看当前已经开放的端口。
```sh
netstat -ntlp
```
14、如果不存在3306，那么通过以下2个命令打开3306端口号。
将3306端口添加到防火墙例外并重启。
```sh
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload
```
15、再次尝试navicat能连接了不。


# python3和pip3的安装
1、安装各种需要的依赖包和组件。
```sh
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make
yum install libffi-devel
```
2、把下载好的python用filezilla上传到服务器中的/root路径下。
3、解压安装，四个步骤。
```sh
tar -zxvf /root/Python-3.8.0.tgz
cd /root/Python-3.8.0
./configure 
make && make install
```
4、配置环境变量
```sh
mv /usr/bin/python /usr/bin/python.bak
ln -s /usr/local/bin/python3 /usr/bin/python
mv /usr/bin/pip /usr/bin/pip.bak
ln -s /usr/local/bin/pip3 /usr/bin/pip
```
5、验证是否安装成功，两个命令。
```sh
python -V
pip -V
```
6、配置yum，不然这个就不能用了。
把第一行的python改为python2.7
```sh
vi /usr/libexec/urlgrabber-ext-down
```
把第一行的python改为python2.7
```sh
vi /usr/bin/yum
```




# 在Linux上离线安装redis
1、用filezilla 把安装包上传到服务器中。
2、解压，然后把解压的文件夹移动到/user中
tar xzf redis-2.8.17.tar.gz
3、进入redis的文件夹，然后执行make命令，进行安装。
cd redis-2.8.17
make
3、安装完成后，休息一分钟。
4、进入redis里面的src文件夹中启动redis
./redis-server  &
5、进入redis里面的src文件夹中，使用客户端打开redis
./redis-cli
5、设置密码为123456
config set requirepass 123456
6、退出
exit

# 在Linux上在线安装redis
1、yum install redis
2、启动redis服务 systemctl start redis
3、查看redis状态  systemctl status redis
4、打开redis客户端  redis-cli
5、设置密码为123456
config set requirepass 123456
6、退出
exit




# 前后端分离的python项目搭建
说明：测谈网是一个前后端分离的项目，所以前端和后端的服务是完全分开的。

【测谈网的后端的搭建步骤】
1、安装python3
2、安装MySQL
3、安装redis，记得修改/etc/redis.conf中的bind为0.0.0.0,并设置密码
4、将测谈网的后端代码压缩包上传到服务器。
5、mkdir /usr/ljtest
6、mv 测谈网后端的代码压缩包 /us/ljtest
7、cd /usr/ljtest
8、解压代码的压缩包
9、修改config.py 将mysql和redis的信息改成自己的，IP，账号，密码
10、创建一个叫ljtestdb的数据库，然后选中数据库，执行sql文件。（sql文件在后端压缩包的db文件夹里面）
11、安装项目需要的第三方的包, pip install -r requirements.txt 
12、启动后端服务，gunicorn run:app -c gunconfig.py

【测谈网的前端的搭建步骤】
1、安装jdk
2、安装tomcat
3、把测谈网的前端的代码放到webapps里面去。


【将前端和后端关联起来】
1、进入webapps
2、打开ljindex文件夹中的js文件夹
3、编辑config.js这个文件
将ip改成自己的ip，端口是2333，别修改
4、ljmanage修改同上
5、浏览器打开ip:8080/ljindex访问前台
6、浏览器打开ip:8080/ljmanage访问前台



