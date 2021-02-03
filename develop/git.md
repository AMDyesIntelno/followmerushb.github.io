?> Git 笔记

## 安装&配置Git

`sudo apt install git -y`

`git config --global user.name "username"`

`git config --global user.email "username@example.com"`

## 忽略文件

通过`.gitignore`文件来进行文件/目录的忽略

例如在放置python3代码的文件夹中可以设置`.gitignore`文件来忽略`__pycahe__/`下的所有文件

```.gitignore
__pycache__/
```

>使用文件`.gitignore`可避免项目混乱

`.gitignore`示例可以在[https://github.com/github/gitignore](https://github.com/github/gitignore)中找到

## 初始化仓库

在仓库目录下执行`git init`

得到输出结果`Initialized empty Git repository in /xxxx/xxxx/.git/`

表明Git在仓库目录下初始化了一个空的仓库,Git用来管理仓库的文件都存储在隐藏的`.git/`中

删除`.git/`将会丢失仓库的历史记录

## 检查状态

在仓库目录下执行`git status`

>我的某个仓库

![](https://img.misaka.gq/Notes/develop/git/git_status.png)

>`On branch master`说明当前位于master分支上
>
>`Your branch is up to date with 'origin/master'`说明这个分支与服务器上对应的分支没有偏离
>
>`Changes not staged for commit`指出已跟踪文件的内容发生了变化,但还没有放到暂存区
>
>`Untracked files`指出存在哪些未跟踪的文件,即意味着Git在之前的提交中没有这些文件,Git不会自动将其纳入跟踪范围

## 将文件加入到仓库中

![](https://img.misaka.gq/Notes/develop/git/git_add.png)

>只要在`Changes to be committed`这行下面的，就说明是已暂存状态
>
>如果有标签`new file`意味着这些文件是新添加到仓库中的

## 执行提交

![](https://img.misaka.gq/Notes/develop/git/git_commit&push.png)

>`git commit -m "update"`以拍摄项目的快照,标志`-m`让`Git`将接下来的消息`update`记录到项目的历史记录中,输出表明我们在master分支上,且有一个文件被修改了
>
>`git status`检查状态时,显示位于master分支,且工作目录是干净的
>
>`git push`推送至服务器