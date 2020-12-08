

## Django migration No migrations to apply

###### 如图，这个数据表中app（应用名）为booktest的对应的已存在的name（迁移文件名）有7项，

###### 如果后续建立的迁移名称与图中的迁移文件name相似，

###### 则Django会认为该表已建立且已执行迁移，因此造成后续迁移时No migrations to apply。





#### 有两种方案可以解决；

#####  一、数据库中执行 delete from django_migrations where app=‘your-app-name’;

######  然后执行迁移

######  python manage.py makemigrations

######  python manage.py migrate

 在上图中`app=‘booktest’`

#####  二、修改生成的文件的名称，

###### 如上图name=‘0001initial’，

###### IDE生成的迁移文件如果和其同名，则可以修改成0009initial。

