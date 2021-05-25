# Django 学习

## Django 第一节

### 创建项目

初始化设置，即用一些自动生成的代码配置一个 Django project --即一个 Django项目实列所需要的设置项合集，包括数据库配置、Django配置和应用程序配置。

cd到一个目录(在虚拟环境激活的情况下)

![study6](https://github.com/noooobCoder/studyphotos/blob/main/study6.PNG)

然后django-admin startproject project_name(project的名字，也是目录名) \[directory](可选，指定一个目录)

![study7](https://github.com/noooobCoder/studyphotos/blob/main/study7.PNG)

默认情况下这行代码会在当前的目录或者directory指定的目录下创建一个name目录。

新目录包含manage.py(至于这玩意有啥用就看这个吧<https://docs.djangoproject.com/zh-hans/3.2/ref/django-admin/>)和一个项目包(包含settings.py和其他文件)并且项目包将和目录同为name

例子：

django-admin startproject prject_name /Users/chx/Documents

注：避免使用如django和test这样会产生冲突的名字(好像是因为这些是Django和Python的内部保留字)， 别把代码放在web服务器的根目录

### 用于开发的简易服务器

用cd切换到Django创建的name目录下，运行py manage.py runserver

这就启动了Django自带的用于开发的简易服务器，然后就可以访问<http://127.0.0.1:8000/>

### 创建应用

项目和应用有什么区别？应用是一个专门做某件事的网络应用程序——比如博客系统，或者公共记录的数据库，或者小型的投票程序。项目则是一个网站使用的配置和应用的集合。项目可以包含很多个应用。应用可以被很多个项目使用。

应用可以存放在任何Python路径中定义的路径。以下是在manage.py同级目录下创建投票应用的示例。

在manage.py的同级目录下，运行命令：

py manage.py startapp app_name(自己取，例子里面用的是polls)

这会创建一个polls目录，结构如下：

![study8](https://github.com/noooobCoder/studyphotos/blob/main/study8.PNG)

这个目录结构包括了投票应用的全部内容。

### 编写视图

打开 app_name/views.py, 输入

    from django.http import HttpResponse

    def index(request):

        return HttpResponse("Hello, world. You're at the app_name index.")

要想看到结果，需要将一个URL映射到它

为了创建URLconf(URL配置)，在app_name目录里面新建一个url.py文件。先简单说一下什么是URLconf

![study9](https://github.com/noooobCoder/studyphotos/blob/main/study9.PNG)

在app_name/urls.py中，输入如下代码:

    from django.urls import path

    from . import views

    urlpatterns = [
    path('', views.index, name='index'),
    ]

PS: from . import中的 . 表示当前目录，这里就是app_name的目录下

然后要在根 URLconf 文件中指定我们创建的 app_name.urls 模块。在project_name/urls.py文件的urlpatterns列表里插入一个include(),如下：

    from django.contrib import admin
    from django.urls import include, path

    urlpatterns = [
        path('app_name/', include('app_name.urls')),
        path('admin/', admin.site.urls),
    ]

函数include()允许引用其他URLconfs。每当 Django 遇到 include() 时，它会截断与此项匹配的 URL 的部分，并将剩余的字符串发送到 URLconf 以供进一步处理。

设计 include() 的理念是使其可以即插即用。因为应用(app)有它自己的 URLconf( app_name/urls.py )，他们能够被放在 "/app_name/" ， "/hahaha_app_name/" ，"/wuhu/app_name/"，或者其他任何路径下，这个应用都能够正常工作。
当包括其他URL模式时总是应该使用include(), admin.site,urls时唯一例外。

如此一来,index视图就被添加进了URLconf。可以通过运行服务器来验证： <http://localhost:8000/app_name/>，会能看到"Hello, world. You're at the app_name index." ，这是在 index 视图中定义的。

补：

![study10](https://github.com/noooobCoder/studyphotos/blob/main/study10.PNG)

## Django 第二节
