# git的使用方法总结以及常见错误

> 运用git应该对GitHub有一定发了解，就不介绍了。如果有不了解的盆友们可以参考：https://github.com/AnHuiCFL/PythonRepository/tree/master

## 一、使用git上传文件的方式

#### 0、上传文件开始你需要运行命令来配置你的用户名和邮箱：

> 代码：
>
> ```git
> $ git config --global user.name "你的Github名"
> 
> $ git config --global user.email "xxxxxx@163.com"
> ```

#### 1、使用HTTPS方式上传本地的文件到GitHUb：

> 代码：
>
> ```git
> # 初始化本地仓库
> git init
> # 创建文件/文档
> mkdir 文件名  / touch 文档名
> # 克隆远程仓库
> git clone https://github.com/user/仓库名.git
> # 添加文件到暂存区
> git add 文件	
> # 提交文件到工作区
> git commit –m '对文件/提交的说明'
> # 把文件推送到远程仓库
> git push -u origin master
> ```

#### 2、使用SSH方式上传本地的文件到GitHUb：

> ###### （1）查看/配置密钥
>
> ```git
> # 查看是否已经存在本地公钥
> cat ~/.ssh/id_rsa.pub
> # 没有--> 执行生成本地公钥
> ssh-keygen -t rsa -C "XXXXX@qq.com"
> # 这个指令会要求你提供一个 位置和文件名 去存放键值对和密码，可以一直点击 Enter键去     使用默认值。
> # 提示1：最好的情况是一个密码对应一个ssh key，但也不是非得这样去做，就像上面我们跳过   创建密码这个步骤。
> # 提示2：设置的密码不能被修改，也不可以被获取。
> 
> # 此时你按照上述路径 C:/Users/Admin/.ssh，找到该文件夹
> # 使用记事本打开 id_rsa.pub 文件，直接选中所有内容复制即可。
> # 也可以根据操作系统的不同，选择不同的指令。
> # windows clip < ~/.ssh/id_rsa.pub
> # mac pbcopy < ~/.ssh/id_rsa.pub
> # 执行命令后，此时公钥已经复制到了粘贴板上。
> 
> ```
>
> ###### (2) 开始上传到仓库git
>
> ```
> # 初始化本地仓库
> git init
> # 创建文件/文档
> mkdir 文件名  / touch 文档名
> # 添加远程仓库
> git remote add origin git@github.com:defnngj/hello-world.git  
> # 添加文件到暂存区
> git add 文件	
> # 提交文件到工作区
> git commit –m '对文件/提交的说明'
> # 把文件推送到远程仓库
> git push -u origin master
> ```
>
> ###### (3) 如果你的HTTPS改变成SSH传输
>
> ```git
> # 修改git的remote url
> # 使用命令 git remote -v 查看你当前的 remote url
> git remote -v
> origin https://github.com/someaccount/someproject.git (fetch)
> origin https://github.com/someaccount/someproject.git (push)
> 
> # 复制此ssh链接，然后使用命令 git remote set-url 来调整你的url。
> git remote set-url origin git@github.com:someaccount/someproject.git
> 
> # 然后你可以再用命令 git remote -v 查看一下，url是否已经变成了ssh地址。
> # 然后你就可以愉快的使用git fetch, git pull , git push，再也不用输入烦人的密码了
> 
> ```

## 二、git上传过程中出现的错误和方法

#### 1、GitHub一般是不传输大文件的-->**提示出错信息：error:failedto push som refs to .......**

> ###### git push 时，存在大文件会报错，即使删除大文件后，还会报错。主要是因为大文件存在没有被提交的commit记录里面。
>
> * 我用的一种方法
>
> ```
> git status 查看未被传送到远程代码库的提交状态
> git cherry -v 查看未被传送到远程代码库的提交描述和说明
> git reset origin/master
> 
> ```
>
> ##### [Git撤销&回滚操作(git reset 和 get revert)](https://blog.csdn.net/asoar/article/details/84111841)   这个可以看看！
>
> * 我看到的还没尝试
>
> ```git
> # 先把远程服务器github上面的文件拉下来
> git pullorigin master 
> git pushorigin master
> # 如果出现报错 fatal:Couldn't find remote ref master
> # 或者fatal: 'origin' doesnot appear to be a git repository
> # 以及fatal: Could notread from remote repository.
> # 则需要重新输入
> git remote add origin git@github.com:defnngj/hello-world.git  
> ```

####  2、 Git传输大文件的方法

> #### Step 1：
>
> git lfs install （先按照lfs，也就是large file storage)
>
> git lfs track *.xxx(xxx是文件格式）
>
> git add .gitattributes （率先add .gitattributes）
>
> #### Step 2:
>
> 然后就是常规操作了
>
> git add .
>
> git commit -m “message”
>
> git push -u origin master
>
> **注意，在上传到repository之后，想要clone下来的话，需要使用git lfs clone， 如果只有git clone的话被传到lfs里的文件是无法被clone的，因为它们被储存在lfs的repository里了。**

#### 3、如果输入添加远程仓库失败 **提示出错信息：fatal: remoteorigin already exists.**

> ```
> 先输入git remote rm origin
> 再输入git remote add origin git@github.com:defnngj/hello-world.git 
> 
> 如果输入git remote rm origin 还是报错的话
> error: Could not remove config section'remote.origin'.
> 
> 谨慎使用！ 我没试过！！！
> 修改git config文件的内容  
> 找到你的git的安装路径
> 找到一个名为git config的文件，打开它把里面的[remote "origin"]那一行删掉就好了！
> ```

# 三、其它

1、git   help

> ```
> git help <verb>
> git <verb> --help
> man git-<verb>
> eg: git help config
> ```

2、设置编译器

> ```
> git config --global core.editor vim
> ```

3、查看配置

> ```
> git config --list
> q 键退出
> ```

