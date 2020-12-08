## 一、FastDFS安装

#### 1 安装fastdfs依赖包

1. 解压缩libfastcommon-master.zip
2. 进入到libfastcommon-master的目录中

3. 执行 ./make.sh
4. 执行 sudo ./make.sh install

#### 2 安装fastdfs

1. 解压缩fastdfs-master.zip
2. 进入到 fastdfs-master目录中
3. 执行 ./make.sh
4. 执行 sudo ./make.sh install

#### 3 配置跟踪服务器tracker

1. sudo cp /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf
2. 在/home/python/目录中创建目录 fastdfs/tracker      
mkdir -p /home/python/fastdfs/tracker
3. 编辑/etc/fdfs/tracker.conf配置文件    sudo vim /etc/fdfs/tracker.conf
修改 base_path=/home/python/fastdfs/tracker

empty for bind all addresses of this host

bind_addr=      不写默认本机ip

the tracker server port

port=22122  默认端口号

#### 4 配置存储服务器storage

1. sudo cp /etc/fdfs/storage.conf.sample /etc/fdfs/storage.conf
2. 在/home/python/fastdfs/ 目录中创建目录 storage
	mkdir -p /home/python/fastdfs/storage
3. 编辑/etc/fdfs/storage.conf配置文件  sudo vim /etc/fdfs/storage.conf

修改内容：
base_path=/home/python/fastdfs/storage
store_path0=/home/python/fastdfs/storage
tracker_server=自己ubuntu虚拟机的ip地址:22122

#### 5 启动tracker 和 storage

> sudo service fdfs_trackerd start  （sudo /usr/bin/fdfs_trackerd  /etc/fdfs/tracker.conf)

> sudo service fdfs_storaged start  （sudo /usr/bin/fdfs_storaged /etc/fdfs/storage.conf)

#### 6 测试是否安装成功

1. 配置client客户端

   把/etc/fdfs/的client.conf.sample文件复制一份为client.conf

   ```shell
   sudo cp /etc/fdfs/client.conf.sample /etc/fdfs/client.conf
   
   ```

   编辑/etc/fdfs/client.conf配置文件

   ```shell
    sudo vim /etc/fdfs/client.conf 
    修改内容：
    base_path=/home/python/fastdfs/tracker
    tracker_server=自己ubuntu虚拟机的ip地址:22122
   ```

   测试是否安装成功

   上传文件测试：

   ```shell
   fdfs_upload_file /etc/fdfs/client.conf 要上传的图片文件路径
   ```

    如果返回类似 group1/M00/00/00/wKiZg1zdFK2AHczeAADR6vRXv_Q539.png

    的文件id则说明文件上传成功,并在浏览器中通过 127.0.0.1:8888/id号 

   可以访问到刚刚上传的图片

## 二、安装nginx及fastdfs-nginx-module web服务器 epoll



#### 1、安装nginx时需要先安装依赖包，不然在后面的安装过程可能会报错，首先查看是否安装依赖包

```shell
dpkg -l | grep zlib（下载: http://www.zlib.net/ )  zlib-1.2.8.tar.gz
dpkg -l | grep pcre （下载: http://www.pcre.org/ )  pcre-8.21.tar.gz
dpkg -l | grep openssl  ( 下载: http://www.openssl.org/ )  openssl-1.0.1.tar.gz
```

###### 终端安装依赖包

```shell
sudo apt-get install zlib1g-dev
sudo apt-get install libpcre3 libpcre3-dev 
sudo apt-get install openssl libssl-dev
```

#### 2、安装Nginx 和 fastdfs-nginx-module

- 把 fastdfs-nginx-module-master.zip 拷贝到 /usr/local 目录下解压

  ```shell
  sudo cp ./fastdfs-nginx-module-master.zip /usr/local
  sudo unzip fastdfs-nginx-module-master.zip
  ```

- 在 /usr/local 下新建一个nginx目录，把 nginx-1.8.1.tar.gz拷贝到 /usr/local/nginx 目录下解压

  ```shell
  sudo mkdir -p /usr/local/nginx
  sudo cp ./nginx-1.8.1.tar.gz /usr/local/nginx
  sudo tar -zxvf nginx-1.8.1.tar.gz
  ```

- 进入nginx-1.8.1的目录执行以下命令

  ```shell
  sudo ./configure --prefix=/usr/local/nginx/ --add-module=../../fastdfs-nginx-module-master/src
  sudo ./make 或者 sudo make 
  sudo ./make install 或者 sudo make install
  ```

  注：若在执行sudo ./make 或者 sudo make时报错，就将对应的makefile文件夹中（在 /nginx/nginx-1.8.1/objs） 找到 -Werrori（在第二行） 并去掉 在重新make即可

- 把 fastdfs-nginx-module-master解压后的目录中src下的 mod_fastdfs.conf 拷贝一份到 /etc/fdfs 中

  ```shell
  sudo cp /usr/local/fastdfs-nginx-module-master/src/mod_fastdfs.conf  /etc/fdfs/mod_fastdfs.conf
  ```

- 修改/etc/fdfs/mod_fastdfs.conf 里面的配置

  ```shell
  sudo vim /etc/fdfs/mod_fastdfs.conf
  
  修改内容：
  connect_timeout=10
  tracker_server=自己ubuntu虚拟机的ip地址:22122
  url_have_group_name=true
  store_path0=/home/python/fastdfs/storage
  ```

- 把解压缩的 fastdfs-master/conf 目录中的http.conf 和 mime.types 各拷贝一份到/etc/fdfs 中

  ```shell
  sudo cp /home/cfl/Desktop/fastdfs-master/conf/http.conf /etc/fdfs/http.conf 
  sudo cp /home/cfl/Desktop/fastdfsmaster/conf/mime.types /etc/fdfs/mime.types
  ```

- 修改 /usr/local/nginx/conf 下 nginx.conf 中的配置

  ```shell
  sudo vim /usr/local/nginx/conf/nginx.conf
  
  在http部分中添加配置信息如下：
  server {
      listen 8888;
      server_name localhost;
      location ~/group[0-9]/ {
          ngx_fastdfs_module;
      }
      error_page 500 502 503 504  /50x.html;
      location = /50x.html {
      root html;
      }
  }
  ```

  > 进入到 /usr/local/nginx/sbin 启动 nginx
  >
  >  `shell sudo ./nginx 重启命令：sudo ./nginx -s reload`

  > 停止：sudo ./nginx -s stop

## 三、使用python客户端上传测试

#### 	1、进入虚拟环境：workon 虚拟环境名称

#### 	2、进入fdfs_client-py-master.zip所在目录，用下面命令安装1				

​		   pip install fdfs_client-py-master.zip

​	注：Windows环境下安装fdfs_client-py-master.zip需要修改里面的配置   

1. 修改 fdfs_client-py-master/fdfs_client 目录下的storage_client.py
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516171531703.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pY2hhZWxfeHdi,size_16,color_FFFFFF,t_70)
2. 修改 fdfs_client-py-master [目录下的setup.py](http://xn--setup-bi1hv64hv02bg1a.py)
   

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516171418758.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pY2hhZWxfeHdi,size_16,color_FFFFFF,t_70)

测试成功：

```python
>>> from fdfs_client.client import Fdfs_client
>>> client = Fdfs_client('/etc/fdfs/client.conf')
>>> ret = client.upload_by_filename('test')
>>> ret
{'Group name':'group1','Status':'Upload successed.', 'Remote file_id':'group1/M00/00/00/
	wKjzh0_xaR63RExnAAAaDqbNk5E1398.py','Uploaded size':'6.0KB','Local file name':'test'
	, 'Storage IP':'192.168.243.133'}
```



```
>>> from fdfs_client.client import *
>>> client = get_tracker_conf('/etc/fdfs/client.conf')

```



## 四、特别注意

### 0、【新人填坑022】fdfs服务器部署时出错：Failed to start fdfs_trackerd.service: Unit fdfs_trackerd.service not found.

https://www.pianshen.com/article/86081584463/

### 1、fdfs踩坑全记录

https://blog.csdn.net/Jacky_kplin/article/details/103014112

0）调取的不是这个在venv下面的client.py文件，而是在python3.5下面的dist-packages下面的client.py文件。

```
错误代码:configparser.NoOptionError:No option ‘connect_timeout’ in section: ‘config’
```

1）创建client实例对象的时候不能直接传入配置文件的地址字符串,否则报错.

```
错误代码:TypeError: type object argument after ** must be a mapping, not str
```

通过模块内**get_tracker_conf**函数, 获取配置文件后传入.

* 我们传入的文件有没有被读取出来？

  我们传入的是`一个字符串`，而且使用的是python3解释器，在执行if  isinstance(filenams, basestring)这一行肯定是不满足的，甚至会报错，即使它不报错，往下继续执行，到了`for  filename in  filenames`这一行也会报错，因为我们传入的`filenames是str类型的`，不是list类型，无法便历，于是在try这里就报错，直接跳到了else，把filename添加到read_ok = []这个空列表中，因此我们最后得到的就是[’/etc/fdfs/client.conf’]。

* 看下basestring是什么类型的数据？
   basestring是str和unicode的超类（父类），也是抽象类，因此不能被调用和实例化，但可以被用来判断一个对象是否为str或者unicode的实例，isinstance(obj, basestring)等价于isinstance(obj, (str, unicode))；

* 在python2解释器下，得到了true的结果，说明这个basetring的类型是仅存在python 2以前的版本的，在python 3 环境下用它判断，就会报错
* `不太理解请看原文详细分析`

2）上传成功后返回的字典内, 其中'Remote file_id'键对应的值由旧版模块string类型更改为byte类型.
则, 返回的文件id是byte类型

如果项目中有自定义上传类, 需要解码返回的文件id为字符串,否则服务器报错.

```
错误代码:a bytes-like object is required, not 'str'
```

### 2、Python3与FastDFS交互(py3Fdfs)

https://www.cnblogs.com/jrri/p/11570089.html

* 现有的FastDFS数据库与python交互, 多是通过下载fdfs-client-py,pip本地安装. 
  不过该模块只适用于Python2, 且适配FastDFS版本较为古老，可能py3不兼容。

* 在Python3中, 有相对应的py3fdfs模块

### 3、这里[py3fdfs](https://pypi.org/project/py3Fdfs/)，直接pip install py3fdfs安装，好，入坑开始

https://blog.csdn.net/fei2636/article/details/104872748

### 4、fastdfs中tracker、storage、client的理解与学习

https://blog.csdn.net/u011186019/article/details/53449138 