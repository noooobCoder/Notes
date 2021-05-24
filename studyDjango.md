# Django

## 创建项目

初始化设置，即用一些自动生成的代码配置一个 Django project --即一个 Django项目实列所需要的设置项合集，包括数据库配置、Django配置和应用程序配置。

cd到一个目录(在虚拟环境激活的情况下)

![study6](https://github.com/noooobCoder/studyphotos/blob/main/study6.PNG)

然后django-admin startproject name(project的名字，也是目录名) \[directory](可选，指定一个目录)

![study7](https://github.com/noooobCoder/studyphotos/blob/main/study7.PNG)

默认情况下这行代码会在当前的目录或者directory指定的目录下创建一个name目录。

新目录包含manage.py(至于这玩意有啥用就看这个吧<https://docs.djangoproject.com/zh-hans/3.2/ref/django-admin/>)和一个项目包(包含settings.py和其他文件)并且项目包将和目录同为name

例子：

django-admin startproject myprject /Users/chx/Documents

注：避免使用如django和test这样会产生冲突的名字(好像是因为这些是Django和Python的内部保留字)， 别把代码放在web服务器的根目录

## 用于开发的简易服务器

用cd切换到Django创建的name目录下，运行py manage.py runserver

这就启动了Django自带的用于开发的简易服务器，然后就可以访问<http://127.0.0.1:8000/>

## 创建应用

项目和应用有什么区别？应用是一个专门做某件事的网络应用程序——比如博客系统，或者公共记录的数据库，或者小型的投票程序。项目则是一个网站使用的配置和应用的集合。项目可以包含很多个应用。应用可以被很多个项目使用。

应用可以存放在任何Python路径中定义的路径。以下是在manage.py同级目录下创建投票应用的示例。

在manage.py的同级目录下，运行命令：

py manage.py startapp polls(自己取，例子里面用的是polls)

这会创建一个polls目录，结构如下：

![study8](https://github.com/noooobCoder/studyphotos/blob/main/study8.PNG)
