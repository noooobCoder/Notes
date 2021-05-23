# Git

## Git clone

### 克隆仓库的命令格式

git clone [url]

git clone \<repo\>

### 克隆到指定目录

git clone \<repo\> \<dirctory\>

### 说明

repo: Git 仓库

directory: 指定目录

### example

git clone <https://github.com/TeaBreak-Tech/EventCalendar-BackendAPI.git>

### 或者

git clone git@github.com:TeaBreak-Tech/EventCalendar-BackendAPI.git

### 或

$ git clone <https://github.com/tianqixin/runoob-git-test> another-runoob-name

Cloning into 'another-runoob-name'...

## Git add

将文件添加到暂存区，添加一个或多个:

git add [file1] [file2] ...

添加指定目录到暂存区，包括子目录:

git add [dir]

添加当前目录下的所有文件到暂存区:

git add .

示例:

![study1](https://github.com/noooobCoder/studyphotos/blob/main/study1.PNG)

![study2](https://github.com/noooobCoder/studyphotos/blob/main/study2.PNG)

## Git status

git status命令用于查看在你上次提交之后是否有对文件进行再次修改。

通常在git status 后加 -s 参数来获得简短的输出结果

示例：

![study3](https://github.com/noooobCoder/studyphotos/blob/main/study3.PNG)

## Git pull

git pull命令用于从远程获取代码并合并本地的版本

是 git fetch 和 git merge FETCH_HEAD 的简写

命令格式如下：

git pull \<远程主机名\> \<远程分支名\>:\<本地分支名\>

比如我们要将远程主机origin的master分支拉取过来，与本地的brantest分支合并：

git pull origin master:brantest

如果远程分支是与当前分支合并，则冒号后面的部分可以省略

git pull origin master

上面命令表示，取回origin/master分支，再与本地的brantest分支合并。

## Git push

git push命令用于从本地的分支版本上传到远程并合并

命令格式如下：

git push \<远程主机名\> \<本地分支名\>:\<远程分支名\>

如果本地分支名与远程分支名相同，则可以省略冒号:

git push \<远程主机名\> \<本地分支名\>

实列：
将本地的master分支推送到origin主机的master分支

$ git push origin master

相当于

$ git push origin master:master

如果本地版本与远程版本有差异，但又要强制推送可以使用 --force参数:

git push --force origin master

删除主机的分支可以使用 --delete参数，以下是删除主机origin的master分支

git push origin --delete master

![study4](https://github.com/noooobCoder/studyphotos/blob/main/study4.PNG)
