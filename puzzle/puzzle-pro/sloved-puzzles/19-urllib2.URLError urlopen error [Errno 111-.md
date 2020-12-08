
urllib2.URLError: <urlopen error [Errno 111
2019-07-27 实战 0 评论
问题

python报错信息如下：
urllib2.URLError: <urlopen error [Errno 111] Connection refused>
原因

代理问题，猜测可能是之前设置ss代理导致的。
解决

在当前终端环境，查找代理，并临时unset即可

命令：

export | grep "proxy"

和

export | grep "PROXY"

显示结果：

declare -x ftp_proxy=":8080"
declare -x http_proxy="127.0.0.1:8888"
declare -x https_proxy="127.0.0.1:8888"
declare -x socks_proxy="127.0.0.1:8080"

unset命令：

unset http_proxy

和

unset https_proxy

成功

再次运行python代码，不再报错。