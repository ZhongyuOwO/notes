## Linux

### CentOS

#### Unix系统通用(Linux&MacOS)
##### 使用快捷方式
例如数据`ssh120`就登录ip为120的服务器
* 先生成ssh密钥步骤跟Git的生成是一样的
```
ssh-keygen -t rsa -C "你的Email"
```
* 进入你的跟目录查看生成的SSH 并复制
```
cat ~/.ssh/id_rsa.pub
```
* 进入服务器的相同的目录
```
cd ~/.ssh/
```
* 修改`known_hosts` 可以用vi或vim
把刚才生成的SSH密钥复制进去保存

* 编辑当前电脑的配置文件
```
alias ssh224='ssh root@192.168.1.224'
alias ssh224='ssh -p 58404 root@192.168.0.224'
```
上面第一行的意思是以root用户登录服务器
第二行是服务器端口使用28404后登录的方式

* 使用的方式是
```
ssh224
```

* 也可以以此方式进入文件目录 如：输入cdr
```
alias cdr='cd /home/doc/'
```

#### 文件上传下载
* 文件上传
```
scp /本地文件地址 远程服务器:/文件目录
scp /Users/zhongyu/Downloads/EC.json root@192.168.0.1:/home/master/zhongyu/
下面的-P是端口
scp -P58404 /Users/zhongyu/Downloads/EC.json root@192.168.0.1:/home/master/zhongyu/
```

* 文件下载
```
scp 远程服务器:/文件地址 /本地文件目录
scp root@192.168.0.1:/tmp/20170905134034.csv /Users/zhongyu/Downloads/
下面的-P是端口
scp -P58404 root@192.168.0.1:/tmp/20170905134034.csv /Users/zhongyu/Downloads/
```

#### 使用阿里云的源
* 备份原来的配置
```
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
```
* 下载源
```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget -P /etc/yum.repos.d/ http://mirrors.aliyun.com/repo/epel-7.repo
```
* 清除原有的缓存
```
yum clean all
```
* 生成新的缓存yum
```
yum makecache
```

#### JDK
* 解压JDK
```
tar -zxvf jdk-8u101-linux-x64.tar.gz -C /home/Java
```
* 编辑环境变量
```
vi /etc/profile
```
* 添加以下环境
```
JAVA_HOME=Java的解压目录如:/home/jdk1.8.0_141
JRE_HOME=$JAVA_HOME/jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export JAVA_HOME JRE_HOME PATH CLASSPATH
```
* 保存本退出键盘操作
```
Ecs  --->  Shift + :  --->  wq
```
* 刷新环境
```
source /etc/profile
```
* 显示Java版本
```
java -version
```

#### MySQL ContOS 7

* 下载源
```
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
```
* 安装rpm包

```
sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
```

* 安装MySQL
```
sudo yum install mysql-server
```

* 登录MySQL
```
mysql -u root
```

* 上面命令可能会执行失败 so 执行下面这个完成
```
sudo chown -R openscanner:openscanner /var/lib/mysql
```
* 重启MySQL服务
```
service mysqld restart
```
* 登录MySQL
```
mysql -u root
```
* 设置密码
```
set password for 'root'@'localhost' = password('123456');
```
* 设置远程登录用户和密码
```
grant all privileges on *.* to 'root'@'%' identified by '123456';
```
* 刷新
```
flush privileges;
```

#### iptatble
* 关闭firewall防火墙并停止服务
```
systemctl stop firewalld.service
```
* 禁止firewall开启启动
```
systemctl disable firewalld.service
```
* 安装iptable防火墙
```
yum install iptables-services
```
* 编辑iptable防火墙配置
```
vi /etc/sysconfig/iptables
```
* 添加以下内容
```
# Firewall configuration written by system-config-firewall
# Manual customization of this file is not recommended.
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 6379 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
```
* 重启防火墙使配置生效
```
systemctl restart iptables.service
```
* 设置防火墙开机自启
```
systemctl enable iptables.service
```
