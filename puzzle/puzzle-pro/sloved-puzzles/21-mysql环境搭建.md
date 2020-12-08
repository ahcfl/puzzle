1.1 服务器端安装 安装服务器端：在终端中输⼊如下命令，回⻋后，然后按照提示输⼊
 sudo apt-get install mysql-server 
并且设置成了开机⾃启动 
服务器⽤于接收客户端的请求、执⾏sql语句、管理数据
服务器端⼀般以服务⽅式管理，名称为mysql 
启动服务 sudo service mysql start 
查看进程中是否存在mysql服务  ps ajx | grep mysql
停⽌服务 sudo service mysql stop 
重启服务 sudo service mysql restart

1.2 MySQL配置⽂件 -了解 配置⽂件⽬录为/etc/mysql/mysql.cnf 进⼊conf.d⽬录，
打开mysql.cnf，发现并没有配置 进⼊mysql.conf.d⽬录，打开mysql.cnf，可以看到配置项

主要配置项如下 bind-address表示服务器绑定的ip，
默认为127.0.0.1 port表示端⼝，
默认为3306 datadir表示数据库⽬录，
默认为/var/lib/mysql general_log_file表示普通⽇志，
默认为/var/log/mysql/mysql.log log_error表示错误⽇志，
默认为/var/log/mysql/error.log

1.3.2 命令⾏客户端 在终端运⾏如下命令，
按提示填写信息 sudo apt-get install mysql-client  
详细连接的命令可以查看帮助⽂档 mysql --help 最基本的连接命令如下，
输⼊后回⻋ mysql -u root -pmysql
按ctrl+d或输⼊如下命令退出 quit 或者 exit




C1LaclLVLILudMaCILIci-pmySqL-u1O0L-PU9Z9

mysql: [Warning] Using a password on the command line inter face can be insecure .
MySQL：[警告]在命令行InterFaces上使用密码可能是不安全的。
Welcome to the MySQL monitor. Commands end with ; or \g.
欢迎来到MySQL监视器。命令以；或\g结尾。
Your MySQL connection id is 5
您的MySQL连接id为5。
Server version: 5.7 .32- 0ubuntuo.16.04.1 (Ubuntu)
服务器版本：5.7-0ubuntuo.16.04.1(Ubuntu)
Copyright (c) 2000, 2020，Oracle and/or its affiliates. All rights reserved.
Copyright (c) 2000, 2020，Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
甲骨文是甲骨文公司和/或其注册商标
affiliates. other names may be trademarks of their respective
附属公司。其他名称可能是它们各自的商标。
owners .
业主。
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement .
键入“Help；”或“\h”以表示帮助。键入‘\c’以清除当前输入语句。
mysql>|
MySQL>\x{e76f}


