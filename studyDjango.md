# Django 学习

## Django 第一节:Request and response

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

补：path的具体内容<https://docs.djangoproject.com/zh-hans/3.2/ref/urls/#django.urls.path>

![study10](https://github.com/noooobCoder/studyphotos/blob/main/study10.PNG)

## Django 第二节:Models and the admin site

### 数据库配置

打开project_name/settings.py。这是个包含了Django项目设置的Python**模块**。

这个配置文件使用SQLite作为默认数据库。若要使用其他数据库，需要安装合适的[database bindings](https://docs.djangoproject.com/zh-hans/3.2/topics/install/#database-installation),然后改变设置文件中DATABASES 'default'项目中的一些键值:[看这个吧](https://docs.djangoproject.com/zh-hans/3.2/intro/tutorial02/)

编辑project_name/settings.py前，先把TIME_ZONE(也在这个setting文件里面) 改为自己的时区：

改前
![study12](https://github.com/noooobCoder/studyphotos/blob/main/study12.PNG)

改后
![study11](https://github.com/noooobCoder/studyphotos/blob/main/study11.PNG)

在setting.py文件中，会看到
![study13](https://github.com/noooobCoder/studyphotos/blob/main/study13.PNG)

![study14](https://github.com/noooobCoder/studyphotos/blob/main/study14.PNG)

默认开启的某些应用至少需要一个数据表，所以，使用他们前需要在数据库中创建一些表，在激活的虚拟环境下的project_name目录下执行

1. py manage.py migrate ①

migrate命令检查INSTALLED_APPS设置，为其中的每个应用创建需要的数据表(database tables)。创建什么取决于project_name/settings.py 设置文件和每个应用的数据库迁移文件。

如果你不需要某个或某些应用，你可以在运行 migrate 前毫无顾虑地从 INSTALLED_APPS 里注释或者删除掉它们。 migrate 命令只会为在 INSTALLED_APPS 里声明了的应用进行数据库迁移。

### 创建模型(Creating models)

在Django里写一个数据库驱动的Web应用的第一步是定义models-也就是数据库结构设计和附加的其他元数据

现在假设我们的project_name是mysite，app_name是polls(投票)。

有时候看英文更好理解：

In our polls app, we’ll create two models: Question and Choice. A Question has a question and a publication date. A Choice has two fields: the text of the choice and a vote tally. Each Choice is associated with a Question.

These concepts are represented by Python classes.

1. Edit the polls/models.py file so it looks like this:

        from django.db import models


        class Question(models.Model):
            question_text = models.CharField(max_length=200)
            pub_date = models.DateTimeField('date published')


        class Choice(models.Model):
            question = models.ForeignKey(Question, on_delete=models.CASCADE)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)
    ②

here, each model is represented by a class that subclasses **django.db.models.Model**. Each model has a number of class variables, each of which represents a database field(数据库字段) in the model.

Each field is represented by an instance(实列) of a Field class - e.g., CharField for character(字符) fields and DateTimeField for datetimes. This tells Django what type of data each field holds.

每个 Field 类实例的变量的名字（例如 question_text 或 pub_date ）也是字段名，所以最好使用对机器友好的格式。你将会在 Python 代码里使用它们，而数据库会将它们作为列名

定义某些 Field 类实例需要参数。例如 CharField 需要一个 max_length 参数。这个参数的用处不止于用来定义数据库结构，也用于验证数据，我们稍后将会看到这方面的内容。

Field 也能够接收多个可选参数；在上面的例子中：我们将 votes 的 default 也就是默认值，设为0。

### 激活模型(Activating models)

上面的一小段用于创建模型的代码给了 Django 很多信息，通过这些信息，Django 可以：

* 为这个应用创建数据库 schema（生成 CREATE TABLE 语句）。
* 创建可以与 Question 和 Choice 对象进行交互的 Python 数据库 API。

但是首先得把 polls 应用安装到我们的项目里

1. To include the app in our project, we need to add a reference to its configuration class in the INSTALLED_APPS setting. 因为 PollsConfig 类写在文件 polls/apps.py 中，所以它的点式路径是 'polls.apps.PollsConfig'。在文件 mysite/settings.py 中 INSTALLED_APPS 子项添加点式路径后，它看起来像这样：

        INSTALLED_APPS = [
            'polls.apps.PollsConfig',
            'django.contrib.admin',
            'django.contrib.auth',
            'django.contrib.contenttypes',
            'django.contrib.sessions',
            'django.contrib.messages',
            'django.contrib.staticfiles',
        ]

    ③

Now Django knows to include the polls app.

接着运行下面的命令：

1. py manage.py makemigrations polls ④

you will see something like:

    Migrations for 'polls':
      polls/migrations/0001_initial.py
        - Create model Question
        - Create model Choice

By running makemigrations, you’re telling Django that you’ve made some changes to your models (in this case, you’ve made new ones) and that you’d like the changes to be stored as a migration.

Migrations are how Django stores changes to your models (and thus your database schema) - they’re files on disk. You can read the migration for your new model if you like; it’s the file polls/migrations/0001_initial.py. Don’t worry, you’re not expected to read them every time Django makes one, but they’re designed to be human-editable in case you want to manually tweak how Django changes things.

1. Now, run migrate again to create those model tables in your database:

    py manage.py migrate ⑤

这个 migrate 命令选中所有还没有执行过的迁移（Django 通过在数据库中创建一个特殊的表 django_migrations 来跟踪执行过哪些迁移）并应用在数据库上 - 也就是将你对模型的更改同步到数据库结构上。

现在，你只需要记住，改变模型需要这三步：

* 编辑 models.py 文件，改变模型。
* (提问，不用在settings里添加 'app_name.apps.App_nameConfig'么？噢，原来这个在创建模型的时候已经完成过了，不用再次操作)
* 运行 python manage.py makemigrations 为模型的改变生成迁移文件。
* 运行 python manage.py migrate 来应用数据库迁移。

### 初试 API

Once you’ve created your data models, Django automatically gives you a database-abstraction API that lets you create, retrieve, update and delete objects.(一旦创建 数据模型 后，Django 自动给予你一套数据库抽象 API，允许你创建，检索，更新和删除对象)

通过以下命令打开 Python 命令行：

py manage.py shell

(我们使用这个命令而不是简单的使用“python”是因为 manage.py 会设置 DJANGO_SETTINGS_MODULE 环境变量，这个变量会让 Django 根据 mysite/settings.py 文件来设置 Python 包的导入路径。)

    >>> from polls.models import Choice, Question  # Import the model classes we just wrote.

    # No questions are in the system yet.
    >>> Question.objects.all()
    <QuerySet []>

    # Create a new Question.
    # Support for time zones is enabled in the default settings file, so
    # Django expects a datetime with tzinfo for pub_date. Use timezone.now()
    # instead of datetime.datetime.now() and it will do the right thing.
    >>> from django.utils import timezone
    >>> q = Question(question_text="What's new?", pub_date=timezone.now())

    # Save the object into the database. You have to call save() explicitly.
    >>> q.save()

    # Now it has an ID.
    >>> q.id
    1

    # Access model field values via Python attributes.
    >>> q.question_text
    "What's new?"
    >>> q.pub_date
    datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

    # Change values by changing the attributes, then calling save().
    >>> q.question_text = "What's up?"
    >>> q.save()

    # objects.all() displays all the questions in the database.
    >>> Question.objects.all()
    <QuerySet [<Question: Question object (1)>]>

<Question: Question object (1)> 对于我们了解这个对象的细节没什么帮助。让我们通过编辑 Question 模型的代码（位于 polls/models.py 中）来修复这个问题。给 Question 和 Choice 增加 __str__() 方法。

    from django.db import models

    class Question(models.Model):
        # ...
        def __str__(self):
            return self.question_text

    class Choice(models.Model):
        # ...
        def __str__(self):
            return self.choice_text

给模型增加 __str__() 方法是很重要的，这不仅仅能给你在命令行里使用带来方便，Django 自动生成的 admin 里也使用这个方法来表示对象。

让我们再为此模型添加一个自定义方法：

    import datetime

    from django.db import models
    from django.utils import timezone


    class Question(models.Model):
        # ...
        def was_published_recently(self):
            return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

新加入的 import datetime 和 from django.utils import timezone 分别导入了 Python 的标准 datetime 模块和 Django 中和时区相关的 django.utils.timezone 工具模块。

## 桥豆麻袋！为什么这里不用按照改变模型的步骤？

保存文件然后通过 python manage.py shell 命令再次打开 Python 交互式命令行：

    >>> from polls.models import Choice, Question

    # Make sure our __str__() addition worked.
    >>> Question.objects.all()
    <QuerySet [<Question: What's up?>]>

    # Django provides a rich database lookup API that's entirely driven by
    # keyword arguments.
    >>> Question.objects.filter(id=1)
    <QuerySet [<Question: What's up?>]>
    >>> Question.objects.filter(question_text__startswith='What')
    <QuerySet [<Question: What's up?>]>

    # Get the question that was published this year.
    >>> from django.utils import timezone
    >>> current_year = timezone.now().year
    >>> Question.objects.get(pub_date__year=current_year)
    <Question: What's up?>

    # Request an ID that doesn't exist, this will raise an exception.
    >>> Question.objects.get(id=2)
    Traceback (most recent call last):
        ...
    DoesNotExist: Question matching query does not exist.

    # Lookup by a primary key is the most common case, so Django provides a
    # shortcut for primary-key exact lookups.
    # The following is identical to Question.objects.get(id=1).
    >>> Question.objects.get(pk=1)
    <Question: What's up?>

    # Make sure our custom method worked.
    >>> q = Question.objects.get(pk=1)
    >>> q.was_published_recently()
    True

    # Give the Question a couple of Choices. The create call constructs a new
    # Choice object, does the INSERT statement, adds the choice to the set
    # of available choices and returns the new Choice object. Django creates
    # a set to hold the "other side" of a ForeignKey relation
    # (e.g. a question's choice) which can be accessed via the API.
    >>> q = Question.objects.get(pk=1)

    # Display any choices from the related object set -- none so far.
    >>> q.choice_set.all()
    <QuerySet []>

    # Create three choices.
    >>> q.choice_set.create(choice_text='Not much', votes=0)
    <Choice: Not much>
    >>> q.choice_set.create(choice_text='The sky', votes=0)
    <Choice: The sky>
    >>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

    # Choice objects have API access to their related Question objects.
    >>> c.question
    <Question: What's up?>

    # And vice versa: Question objects get access to Choice objects.
    >>> q.choice_set.all()
    <QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
    >>> q.choice_set.count()
    3

    # The API automatically follows relationships as far as you need.
    # Use double underscores to separate relationships.
    # This works as many levels deep as you want; there's no limit.
    # Find all Choices for any question whose pub_date is in this year
    # (reusing the 'current_year' variable we created above).
    >>> Choice.objects.filter(question__pub_date__year=current_year)
    <QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

    # Let's delete one of the choices. Use delete() for that.
    >>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
    >>> c.delete()

## 介绍Django管理页面

### 创建一个管理员账号

首先，我们得创建一个能登录管理页面的用户。请运行下面的命令：

py manage.py createsuperuser

键入想要使用的用户名，然后按下回车：

Usernam：admin

然后提示你输入想要使用的邮箱地址：

Email address：admin@example.com

最后输入密码，注意，密码是隐形的，不要以为是你没有输入，记住密码按回车就行了

Password:
Password (again):
Superuser created successfully.

### 启动开发服务器

py manage.py runserver

现在，打开浏览器，转到你本地域名的 "/admin/" 目录， -- 比如 "http://127.0.0.1:8000/admin/" 。你应该会看见管理员登录界面：

![image](https://docs.djangoproject.com/zh-hans/3.2/_images/admin01.png)

### 进入管理站点页面

现在，试着使用你在上一步中创建的超级用户来登录。然后你将会看到 Django 管理页面的索引页：

![image](https://docs.djangoproject.com/zh-hans/3.2/_images/admin02.png)

### 向管理页面中加入投票应用

但是我们的投票应用在哪呢？它没在索引页面里显示。

只需要再做一件事：我们得告诉管理，问题 Question 对象需要一个后台接口。打开 polls/admin.py 文件，把它编辑成下面这样:

    from django.contrib import admin

    from .models import Question

    admin.site.register(Question)

### 体验便捷的管理功能

现在我们向管理页面注册了问题 Question 类。Django 知道它应该被显示在索引页里：

![image](https://docs.djangoproject.com/zh-hans/3.2/_images/admin03t.png)
