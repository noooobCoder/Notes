# Django 学习

## 创建项目

0. 配置虚拟环境:
转移到想要创建项目的文件夹

        ...\> py -m venv project-name(-会自动变成_)

1. 激活虚拟环境:

        ...\> project-name\Scripts\activate.bat

2. cd到另一个目录

        ...\> django-admin startproject mysite
    会在当前目录创建一个mysite文件夹

## 启动服务器

cd到Django创建的mysite目录下，运行py manage.py runserver

## 创建应用

在manage.py的同级目录下，运行命令：

    py manage.py startapp app_name

例子里面的app name是polls，就是投票，这会创建一个polls目录

## Views相关

思路： 用URL映射到views
网页 <https://djangobook-cn.readthedocs.io/en/latest/chapter03.html> 有很详细的介绍

1. 编写视图：

        from django.http import HttpResponse


        def view_function(request):
            return HttpResponse("Hello, world.")

    * 其中request是一个对象，包含了触发这个 view function (view_function 是统称，名字随便取) 的 Web 请求的信息， 它是 django.http.HttpRequest 的一个 instance
    * 这个函数返回一个 HttpResponse 对象，这个对象包含了文本 “Hello world”

2. 关联 view function 到一个 URL(显式地告诉Django我们要让哪个URL来激活这个视图) 需要用到 URLconf：是一个URL和这个URL将调用的view function之间的映射关系。
这里的方式十分多样，可以直接import views文件，也可以import各个views文件中的view function。

        '''
            Examples:
        Function views
            1. Add an import:  from my_app import views
            2. Add a URL to urlpatterns:  
            urlpatterns = [
                path('', views.home, name='home'),
                ...
            ]
        Class-based views
            1. Add an import:  from other_app.views import Home
            2. Add a URL to urlpatterns: 
            urlpatterns = [
                path('', Home.as_view(), name='home'),
                ...
            ]
        Including another URLconf
            1. Import the include() function: from django.urls import include, path
            2. Add a URL to urlpatterns:  
            urlpatterns = [
                path('blog/', include('blog.urls')),
                ...
            ]
        '''
    path函数作用见 <https://www.cnblogs.com/xiaohaodeboke/p/11842668.html>

3. 在根 URLconf 文件中指定我们创建的 polls.urls 模块
在 mysite/urls.py 文件的 urlpatterns 列表里插入一个 include()
include()的具体用法 <https://www.codenong.com/cs107099438/>
当然其实不用include也有别的方法

        from django.contrib import admin
        from django.urls import include, path

        urlpatterns = [
            path('polls/', include('polls.urls')),
            path('admin/', admin.site.urls),
        ]

后续详见<https://docs.djangoproject.com/zh-hans/3.2/intro/tutorial03/>
还有简化与报错，会用到
render()

    from django.shortcuts import render

Http404

    from django.http import Http404

甚至Http404还可以简化为快捷函数：get_object_or_404()

    from django.shortcuts import get_object_or_404, render

## 数据库配置

编辑project_name/settings.py前，先把TIME_ZONE(也在这个setting文件里面) 改为自己的时区：

## Models相关

1. 编辑 models.py 文件，改变模型

2. 一旦你定义了你的模型，你需要告诉 Django 你准备使用这些模型。你需要修改settings文件中的 INSTALLED_APPS ，在这个设置中添加包含 models.py 文件的模块名称。
例如，若模型位于项目中的 app01.models 模块（ 此包结构由 manage.py startapp 命令创建）， INSTALLED_APPS 应设置如下：
    \#打开settings.py来注册app

    \#方式1：直接加入app名，

        INSTALLED_APPS = [
            'django.contrib.admin',
            'app01',
        ]
    \#方式2：直接加入app对应的AppConfig子类

        INSTALLED_APPS = [
            'django.contrib.admin',
            'app01.apps.App01Config',
        ]

3. 运行 python manage.py makemigrations app01 为模型的改变生成迁移文件。(其实就是先看看有没有改动，可以不用) **运行python manage.py migrate 来应用数据库迁移。**

**注意**：makemigrate是当对模型有增删修改后用于将改动**应用到数据库上**，需要migrate的只是对模型的修改，比如增加一个字段(Field)，删除或增加一个模型(Model)，修改类方法(函数)是不用再次重复步骤的

## Tests相关

详见<https://docs.djangoproject.com/zh-hans/3.2/intro/tutorial05/>

1. 发现bug：通过py manage.py shell运行时发现bug

2. 创建test：Django 应用的测试应该写在 app 的 tests.py 文件里。测试系统会自动的在所有以 tests 开头的文件里寻找并执行测试代码。

3. 运行测试

        ...\> py manage.py test polls

### [测试工具之Client](https://docs.djangoproject.com/zh-hans/3.2/intro/tutorial05/#the-django-test-client)

Django 提供了一个供测试使用的 Client 来模拟用户和视图层代码的交互。我们能在 tests.py 甚至是 shell 中使用它。

## 自定义相关
