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

## 桥豆麻袋！为什么这里不用按照改变模型的步骤？？？注意：makemigrate是当对模型有增删修改后用于将改动*应用到数据库上*，需要migrate的只是对模型的修改，比如增加一个字段(Field)，删除或增加一个模型(Model)，修改类方法(函数)是不用再次重复步骤的

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

## Django 第三节 Views and templates

### 编写更多视图

即向 app_name/views.py 里面添加更多视图，除了必须的request外，他们还接收参数 question_id。

    def detail(request, question_id):
        return HttpResponse("You're looking at question %s." % question_id)

    def results(request, question_id):
        response = "You're looking at the results of question %s."
        return HttpResponse(response % question_id)

    def vote(request, question_id):
        return HttpResponse("You're voting on question %s." % question_id)

然后把新视图添加进app_name.urls模块里，只要添加几个url()函数调用就行：

    from django.urls import path

    from . import views

    urlpatterns = [
        # ex: /polls/
        path('', views.index, name='index'),
        # ex: /polls/5/
        path('<int:question_id>/', views.detail, name='detail'),
        # ex: /polls/5/results/
        path('<int:question_id>/results/', views.results, name='results'),
        # ex: /polls/5/vote/
        path('<int:question_id>/vote/', views.vote, name='vote'),
    ]

当浏览器跳转"/polls/34/"(这就是URL), Django 将会运行detail()方法并展示URL中的question_id。至于"/polls/34/result/" 和 "/polls/34/vote/"--则只有暂时用于占位的结果和投票页。

当某人请求你网站的某一页面时——比如说， "/polls/34/" ，Django 将会载入 mysite.urls 模块，因为这在配置项 ROOT_URLCONF 中设置了。然后 Django 寻找名为 urlpatterns 变量并且按序匹配正则表达式。在找到匹配项 'polls/'，它切掉了匹配的文本（"polls/"），将剩余文本——"34/"，发送至 'polls.urls' URLconf 做进一步处理。在这里剩余文本匹配了 '\<int:question_id\>/'，使得我们 Django 以如下形式调用 detail():

    detail(request=<HttpRequest object>, question_id=34)

question_id=34 由 \<int:question_id\> 匹配生成。使用尖括号“捕获”这部分 URL，且以关键字参数的形式发送给视图函数。上述字符串的 :question_id> 部分定义了将被用于区分匹配模式的变量名，而 int: 则是一个转换器决定了应该以什么变量类型匹配这部分的 URL 路径。

### 写个真正有用的视图

写每个视图必做的二事：返回一个包含请求页码内容的HttpResponse对象，或者抛出一个异常，如Http404。

修改 polls/views.py中的index视图：

    from .models import Question

    def index(request):
        latest_question_list = Question.objects.order_by('-pub_date')[:5]
        output = ', '.join([q.question_text for q in latest_question_list])
        return HttpResponse(output)

这里有个问题：页面的设计写死在视图函数的代码里的。如果你想改变页面的样子，你需要编辑 Python 代码

但选择**Django的模板系统**，只要创建一个视图，就可以将页面的设计从代码中分离出来

1. 在app_name目录下创建一个templates(模板))目录(Django将会在这个目录里查找模板文件)
2. 在刚刚创建的templates目录里，在创建一个目录app_name，然后在其中新建一个文件index.html。路径即是app_name/templates/app_name/
3. 更新app_name/views.py里的index视图来使用模板

        from django.http import HttpResponse
        from django.template import loader

        from .models import Question


        def index(request):
            latest_question_list = Question.objects.order_by('-pub_date')[:5]
            template = loader.get_template('polls/index.html')
            context = {
                'latest_question_list': latest_question_list,
            }
            return HttpResponse(template.render(context, request))

上述代码的作用是，载入 polls/index.html 模板文件，并且向它传递一个上下文(context)。这个上下文是一个字典，它将模板内的变量映射为 Python 对象。

用你的浏览器访问 "/polls/" ，你将会看见一个无序列表，列出了我们在 教程第 2 部分 中添加的 “What's up” 投票问题，链接指向这个投票的详情页。

### render()简化

    from django.shortcuts import render

    from .models import Question


    def index(request):
        latest_question_list = Question.objects.order_by('-pub_date')[:5]
        context = {'latest_question_list': latest_question_list}
        return render(request, 'polls/index.html', context)

The render() function takes the **request** object as its **first argument**, **a template name** as its **second argument** and **a dictionary** as its **optional third argument**. *It returns an HttpResponse object of the given template rendered with(用...来表述) the given context*.

### 抛404

1. 普通法

        from django.http import Http404
        from django.shortcuts import render

        from .models import Question

        def detail(request, question_id):
            try:
                question = Question.objects.get(pk=question_id)
            except Question.DoesNotExist:
                raise Http404("Question does not exist")
            return render(request, 'polls/detail.html', {'question': question})

2. 快捷函数：get_object_or_404()

        from django.shortcuts import get_object_or_404, render

        from .models import Question
        # ...
        def detail(request, question_id):
            question = get_object_or_404(Question, pk=question_id)
            return render(request, 'polls/detail.html', {'question': question})

### 使用模板系统

polls/detail.html 模板里正式的代码

    <h1>{{ question.question_text }}</h1>
    <ul>
    {% for choice in question.choice_set.all %}
        <li>{{ choice.choice_text }}</li>
    {% endfor %}
    </ul>

模板系统统一使用点符号来访问变量的属性。在示例 {{ question.question_text }} 中，首先 Django 尝试对 question 对象使用字典查找（也就是使用 obj.get(str) 操作），如果失败了就尝试属性查找（也就是 obj.str 操作），结果是成功了。如果这一操作也失败的话，将会尝试列表查找（也就是 obj[int] 操作）。

在 {% for %} 循环中发生的函数调用：question.choice_set.all 被解释为 Python 代码 question.choice_set.all() ，将会返回一个可迭代的 Choice 对象，这一对象可以在 {% for %} 标签内部使用。

### 去除模板中的硬编码 URL

从

    <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>

到

    <li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>

这样如果要修改视图的URL，就不用在模板里修改任何东西，只用在polls/urls.py 里面修改

### 为URL名称添加命名空间

1. 在根 URLconf 中添加命名空间。在 polls/urls.py 文件中稍作修改，加上 app_name 设置命名空间：

        from django.urls import path

        from . import views

        app_name = 'polls'
        urlpatterns = [
            path('', views.index, name='index'),
            path('<int:question_id>/', views.detail, name='detail'),
            path('<int:question_id>/results/', views.results, name='results'),
            path('<int:question_id>/vote/', views.vote, name='vote'),
        ]

2. 修改polls/index.html文件，从：

        <li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
    修改为指向具有命名空间的详细视图

        <li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>

## Django 第四节 Forms and generic views

forms就看看[那个教程](https://docs.djangoproject.com/zh-hans/3.2/intro/tutorial04/)好了

### 使用通用视图(generic views)

转换成使用通用视图系统，需要做以下几步来完成转换，我们将：

1. 转换 URLconf。
首先，打开 polls/urls.py 这个 URLconf 并将它修改成：

        from django.urls import path

        from . import views

        app_name = 'polls'
        urlpatterns = [
            path('', views.IndexView.as_view(), name='index'),
            path('<int:pk>/', views.DetailView.as_view(), name='detail'),
            path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
            path('<int:question_id>/vote/', views.vote, name='vote'),
        ]

1. 删除一些旧的、不再需要的视图。

1. 基于 Django 的通用视图引入新的视图。

        删除旧的 index, detail, 和 results 视图，并用 Django 的通用视图代替。打开 polls/views.py 文件，并将它修改成：

        from django.http import HttpResponseRedirect
        from django.shortcuts import get_object_or_404, render
        from django.urls import reverse
        from django.views import generic

        from .models import Choice, Question


        class IndexView(generic.ListView):
            template_name = 'polls/index.html'
            context_object_name = 'latest_question_list'

            def get_queryset(self):
                """Return the last five published questions."""
                return Question.objects.order_by('-pub_date')[:5]


        class DetailView(generic.DetailView):
            model = Question
            template_name = 'polls/detail.html'


        class ResultsView(generic.DetailView):
            model = Question
            template_name = 'polls/results.html'


        def vote(request, question_id):
            ... # same as above, no changes needed.

两个通用视图： ListView 和 DetailView 。这两个视图分别抽象“显示一个对象列表”和“显示一个特定类型对象的详细信息页面”这两种概念。

每个通用视图需要知道它将作用于哪个模型。 这由 model 属性提供。
DetailView 期望从 URL 中捕获名为 "pk" 的主键值，所以我们为通用视图把 question_id 改成 pk 。
默认情况下，通用视图 DetailView 使用一个叫做 \<app name\>/\<model name\>_detail.html 的模板。在我们的例子中，它将使用 "polls/question_detail.html" 模板。template_name 属性是用来告诉 Django 使用一个指定的模板名字，而不是自动生成的默认名字。 我们也为 results 列表视图指定了 template_name —— 这确保 results 视图和 detail 视图在渲染时具有不同的外观，即使它们在后台都是同一个 DetailView 。

类似地，ListView 使用一个叫做 \<app name\>/\<model name\>_list.html 的默认模板；我们使用 template_name 来告诉 ListView 使用我们创建的已经存在的 "polls/index.html" 模板。

在之前的教程中，提供模板文件时都带有一个包含 question 和 latest_question_list 变量的 context。对于 DetailView ， question 变量会自动提供—— 因为我们使用 Django 的模型（Question）， Django 能够为 context 变量决定一个合适的名字。然而对于 ListView， 自动生成的 context 变量是 question_list。为了覆盖这个行为，我们提供 context_object_name 属性，表示我们想使用 latest_question_list。作为一种替换方案，你可以改变你的模板来匹配新的 context 变量 —— 这是一种更便捷的方法，告诉 Django 使用你想使用的变量名。
