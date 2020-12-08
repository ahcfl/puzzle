### with  open()   as 的用法 和 with上下文管理器（Context manager）

-----------------------------------------------------------------------------------------------

#### 一、基础读写：

##### 读⽂件：

1、以读的⽅式打开⽂件 

f = open("1.txt", "r") 

2、读取⽂件内容 

str = f.read() 

print(str) 

3、关闭⽂件 

f.close() 

##### 写⽂件：

1、以写的⽅式打开⽂件 

f = open("2.txt", "w") 

2、写⼊⽂件内容 

str = f.write("Welcome to 北京！") 

3、关闭⽂件 

f.close() 

----------------------

#### 二、 错误代码示范： 

##### 这种⽅式存在的问题是什么？

1、以读的⽅式打开⽂件 

f = open("1.txt", "r") 

 2、读取⽂件内容 

f.write("xxxxx") 

f.close() 

##### 由于⽂件读写时都有可能产⽣ IOError ，⼀旦出错，后⾯的 f.close() 就不会调⽤。

--------------------------------

#### 三、优化代码：

##### 1）为了保 证⽆论是否出错都能正确地关闭⽂件，我们可以使⽤ try ... finally 来实现： 

try:

1、以读的⽅式打开⽂件 

f = open("1.txt", "r") 

2、读取⽂件内容 

f.write("xxxxx") 

except IOError as e: 

print("⽂件操作出错", e) 

finally: 

f.close() 

##### 2）Python引⼊了 with 语句来⾃动帮我们调⽤ close() ⽅法： 

try:

1、以读的⽅式打开⽂件 

with open("1.txt", "r") as f: 

2、读取⽂件内容 

f.write("xxxxx") 

except IOError as e: 

print("⽂件操作出错", e) 

》这和前⾯的 try ... finally 是⼀样的，但是代码更佳简洁，并且不必调⽤ f.close() ⽅法。 

》使⽤ with 关键字。open ⽅法的返回值赋值给变量 f，当离开 with 代码块 的时候，系统能够⾃动调⽤ f.close() ⽅法

------

#### 四、 with 的原理之上下文管理器

##### with 的原理前要涉及到另 外⼀个概念，就是上下⽂管理器（Context Manager）。

1）上下文理解成（语言）环境就可以，(⽽且上下⽂虽然叫上下⽂，但是程序⾥⾯⼀ 般都只有上⽂⽽已，只是叫的好听叫上下⽂。。

2）上下⽂管理器本质就是能够⽀持with操作。 任何实现了` __enter__() 和 __exit__()`⽅法的对象都可称之为上下⽂管理器，上下⽂管理器对象可以使 ⽤ with 关键字。显然，⽂件（file）对象也实现了上下⽂管理器协议。 

###### 一、代码演示：

>```python
>"""
>类：  MyFile()
>类方法：
>    1. __enter__()  上文方法
>    2. __exit__()   下文方法
>    3. __init__()   方法，接收参数并且初始化
>
>目的:验证上下⽂管理器（Context Manager）
>    with MyFile('hello.txt', 'r') as file:
>        file.read()
>    
>"""
>
>class MyFile(object):
>
>    # 1. __enter__()  上文方法
>    def __enter__(self):
>        print("进入上文....")
>        # 1，打开文件 
>        self.file = open(self.file_name, self.file_model)
>        # 2，返回打开的文件资源
>        return self.file
>
>    # 2. __exit__()   下文方法
>    def __exit__(self, exc_type, exc_val, exc_tb):
>        print("进入下文....")
>        # 关闭文件资源
>        self.file.close()
>
>    # 3. __init__()   方法，接收参数并且初始化
>    def __init__(self, file_name, file_model):
>        # 局部变量定义成实例属性 -> 在类里面调用
>        # 保存文件名和文件打开模式 -> 到实例属性中
>        self.file_name = file_name
>        self.file_model = file_model
>
>
>if __name__ == '__main__':
>
>    with MyFile("hello.txt", "r") as file:
>        # 开始读取文件
>        file_data = file.read()
>        print(file_data)
>        
>
>```
>
>执行结果：
>
>```python
>进入上文....
>hello,python!
>进入下文.... 
>```

`__enter__()`⽅法返回资源对象，这⾥就是你将要打开的那个⽂件对象，`__exit__()`⽅法做⼀些清除⼯作。 

因为 File 类实现了上下⽂管理器，所以就可以使⽤ with 语句了。

###### 二、实现上下⽂管理器的另外⽅式 

Python 提供了⼀个 contextmanager 模块的装饰器`@contextmanager `，更进⼀步简化了上下⽂管理器的实现⽅式。通过 yield 将函数分割成两部分，yield 之前的语句在` __enter__ ()`⽅法中执⾏，yield 之后的语句在` __exit__()`⽅法中执⾏。紧跟在 yield 后⾯的值是 函数的返回值。

装饰器：不修改（待修饰函数）原函数的代码，增加新的功能（方法）

> ```python
> """
> 思路：
>     def myopen(file_name,file_model)
> 
>             上文（打开资源）
>             yield
>             下文（关闭资源）
> 
> 装饰器装饰函数的步骤：
> 1. 导入模块 from contextlib import contextmanager
> 2. 开始装饰 @contextmanager
> 
> 
> """
> from contextlib import contextmanager
> 
> 
> @contextmanager
> def myopen(file_name,file_model):
> 
>     print("进入上文")
>     # 1.打开文件
>     file = open(file_name,file_model)
>     # 2.返回资源
>     yield file
>     print("进入下文")
>     # 下文
>     # 3. 关闭资源
>     file.close()
> 
> 
> with myopen("hello.txt", "r") as file:
>     file_data = file.read()
>     print(file_data)
>      
> ```

>执行结果：
>
>```python
>进入上文....
>hello,python!
>进入下文.... 
>```

----

#### 五、 总结

1）Python 提供了 with 语法⽤于简化资源操作的后续清除操作，实现原理建⽴在上下⽂管理器协议 (`__enter__`和`__exit__`)之上 

2）with使⽤代码中如果在打开过程中发⽣异常，需要使⽤try-except进⾏捕获 

3）Python 还提供了⼀个 contextmanager 装饰器，更进⼀步简化上下管理器的实现⽅式。 

-----------------------

#### 六、应用with  open()    as

##### 1.创建  写文件

> ```python
>request_data = urllib.request.urlopen(img_url)
> 
>#在(指定路径下 )--> 创建文件 --> 准备保存
>  
>with open('/home/cfl/Desktop/” + file_name, "wb") as file:
> 
>#在(当前目录) --> 创建文件 --> 准备保存
> 
>with open(file_name, "wb") as file:
>           while True:
>                 file_data = request_data.read(1024)
>                 if file_data:
>                     file.write(file_data)
>                 else:
>                     break
>    ```

##### 2.打开 读文件

>     ```python
>  recv_data = new_client_socket.recv(1024)
>     file_name = recv_data.decode()
> #根据文件名读取文件内容
>  try:
> 	with open(file_name, "rb") as file:		
>      把读取的文件内容发送给客户端(循环)
>  		while True:> 			
>             file_data = file.read(1024)
> 			判断是否读取到了文件的末尾
>            	if file_data:
>                 发送文件
>             	new_client_socket.send(file_data)                       
>             else:                   
>                 break
>     except Exception as e:
>     	print("文件%s下载失败" % file_name)
>  else:
>     	print("文件%s下载成功" % file_name
>  ```
>     
> 

