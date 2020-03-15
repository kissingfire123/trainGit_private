

# Git加强学习实践笔记

> 注：笔记教程来源感谢廖雪峰大神:https://www.liaoxuefeng.com/wiki/896043488029600
>
> 本教程环境：Windows7+GitBash
>
> 教程编辑工具：Typora
>
> 编辑时间：2020年3月15日
>
> 作者：BenjaminLee

[TOC]



### 1.安装配置git

git 下载目录： https://git-scm.com/download

安装了git后，在GitBash配置git

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

注意GitBash的几个技巧：

- 是类Linux的命令环境，部分Linux指令可以使用，例如cd ,ls,pwd等；

- CTRL+L 快捷键可以清楚当前屏幕的显示，光标又跳到窗口顶端；过往内容可以鼠标滚轮查看；

- 复制是CTRL+insert；粘贴是SHIFT+insert;

- 方向键上下切换可以找到最近执行过的指令；

- CTRL+A是回到当前命令行的开头；CTRL+E是回到当前命令行的末尾；CTRL+U是剪切/删除当前命令行所有内容；

- git相关的目录路径最好不要包括中文；Git的所有指令都别用DOS窗口。

  

### 2.创建版本库

​	我们选择在E:\Git_Practice目录下创建名为trainGit的版本库(repository)，在该文件夹位置鼠标右键打开GitBash，执行以下命令:

```shell
$ mkdir trainGit
$ cd trainGit
$ pwd
/e/Git_Practice/trainGit
```

**[<u>git init初始化版本库</u>]()**：

```shell
$ git init
Initialized empty Git repository in /e/Git_Practice/trainGit/.git/
```

​	可以发现当前目录下多了一个`.git`的目录（一般是隐藏文件夹），这个目录是Git来跟踪管理版本库的，没事千万不要作死去修改这个目录的文件。

<u>[**git add 添加文件到版本库**]()</u>，现在编写了一个文本文件[GitInro.txt]()，内容如下：

```shell
1.初步认识git
git 下载目录： https://git-scm.com/download

安装了git后，在gitbash配置git
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

执行命令添加到git仓库：

```shell
git add GitIntro.txt
```

**[<u>git commit 把文件提交到仓库</u>]()**：

```shell
$ git commit -m "wrote a GitIntro file"
[master (root-commit) f937cd7] wrote a GitIntro file
 1 file changed, 9 insertions(+)
 create mode 100644 GitInro.txt
```

注意：`-m`后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

`git add` 可以多次添加不同的文件，也可以一次添加多个文件（或者用通配符添加多个文件)，如下，我们一次添加2个文件后再commit：

```shell
$ git add GitInro2.txt GitInro3.txt

$ git commit -m "I add 2 more GitIntro files"
[master 87afdf3] I add 2 more GitIntro files
 2 files changed, 18 insertions(+)
 create mode 100644 GitInro2.txt
 create mode 100644 GitInro3.txt
```



### 3.时光机穿梭

​	修改文件GitInro.txt ，在末尾添加一行'"I modify 1st"，然后[用`git status`查看结果]()：

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   GitInro.txt
```

​	提示有改动没有提交，接下来[用`git diff`查看改动的内容]()：

```shell
$ git diff
diff --git a/GitInro.txt b/GitInro.txt
index fb52f9d..1b7ffcd 100644
--- a/GitInro.txt
+++ b/GitInro.txt
@@ -5,5 +5,7 @@ git 下载目录： https://git-scm.com/download
 $ git config --global user.name "Your Name"
 $ git config --global user.email "email@example.com"

+I modify 1st
+
```

​	然后放心添加和提交，先`git add GitInro.txt`，然后`git commit  -m  "add one mark line at last"`。成功后再次产看status状态，可以发现我们没有任何没添加和提交的改动。

#### 3.1版本回退

[`git log`可以记录所有的历史记录。]()

​	如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数（最左边的串号是commit-id版本号，SHA1值）：

```shell
$ git log --pretty=oneline
a9a2a1b787f69cabeff8b3ffbe58b7c6316d16bb (HEAD -> master) make mark of file2
9bcc55d0360e71c8c105140b618778ee7158b68f add one mark line at last
87afdf341802295999fbab5be1a190cdb08263ba I add 2 more GitIntro files
f937cd78a9057e0e5d71e116127c7cd732711782 wrote a GitIntro file
```

​	在Git中，用`HEAD`表示当前版本，也就是最新的提交`a9a2a...`（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

现在，要把当前版本”make mark of file2"回退到上一版本“add one mark line at last”，需要[执行`git reset`指令进行重置/回退：]()

```shell
$ git reset --hard HEAD^
HEAD is now at 9bcc55d add one mark line at last
```

​	这时候用git log查看，发现原先的`a9a2a...`没有了，别急，只要前几位的commit-id还记得，git可以给你找到放回来：

```shell
$ git reset --hard a9a2a
HEAD is now at a9a2a1b make mark of file2
```

​	这时候问题来了，如果窗口也关了，commit-id也没去手动记住，这时候，需要[`git reflog`出场了，记住你的每一次命令：]()

```shell
$ git reflog
a9a2a1b (HEAD -> master) HEAD@{0}: reset: moving to a9a2a
9bcc55d HEAD@{1}: reset: moving to HEAD^
a9a2a1b (HEAD -> master) HEAD@{2}: commit: make mark of file2
9bcc55d HEAD@{3}: commit: add one mark line at last
87afdf3 HEAD@{4}: commit: I add 2 more GitIntro files
f937cd7 HEAD@{5}: commit (initial): wrote a GitIntro file
```

#### 3.2工作区和暂存区

*Git和其他版本控制系统如SVN的一个不同之处就是有暂存区（Stage）的概念。*

- 工作区（Working Directory）

  就是你在电脑里能看到的目录，比如我的**trainGit**文件夹就是一个工作区（不包括这个名为.git的隐藏文件夹）：

![image-20200315154037410](C:\Users\liwenyao\AppData\Roaming\Typora\typora-user-images\image-20200315154037410.png)

- 版本库（Repository）

  工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

  Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![git-repo](https://www.liaoxuefeng.com/files/attachments/919020037470528/0)

- 

  再复习一遍add和commit的操作，我这边新建一个文件GPL_HeadContent.txt，git status查看状态发现这个文件是”`untracked`“，add之后commit一下，然后再次git status查看状态`“nothing to commit, working tree clean”`，这就对了。

  所以，`git add`命令实际上就是把要提交的所有修改放到暂存区，然后，执行`git commit`就可以一次性把暂存区的所有修改提交到分支。

#### 3.3管理修改

*为什么Git比其他版本控制系统设计得优秀，因为Git跟踪并管理的是修改，而非文件。*

修改包含的意义很广泛，包括新增、删除一个字符/一行内容，添加或者删除文件/文件夹等。

下面针对文件GitInro.txt做个试验：

[第一次修改--->git add ---->第二次修改--->gitcommit]()                 <u>预期：只有第一次修改生效</u>

在末尾添加一行"Git tracks changes."，然后cat指令查看改动后的文件：

```shell
$ cat GitInro.txt
1.初步认识git
git 下载目录： https://git-scm.com/download

安装了git后，在gitbash配置git
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"

I modify 1st

Git tracks changes.
```

改动后执行`git add`，将修改添加到暂存区。

此时做第二次修改，在末尾再添加一行“Git tracks files.”，然后提交:

```shell
$ git commit -m "Git tracks changes or files"
[master c4bdfd8] Git tracks changes or files
 1 file changed, 1 insertion(+)
```

提交后，再看看状态(提示有改动没有添加到stage)：

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   GitInro.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

提交后，用[`git diff HEAD --  GitInro.txt`命令可以查看工作区和版本库里面最新版本的区别]()：

```shell
$ git diff HEAD -- GitInro.txt
diff --git a/GitInro.txt b/GitInro.txt
index 4b1adc7..e808343 100644
--- a/GitInro.txt
+++ b/GitInro.txt
@@ -9,4 +9,4 @@ I modify 1st

 Git tracks changes.

-
+Git tracks files.
\ No newline at end of file
```

还可以[用git diff GitIntro.txt比较工作区和暂存区的区别]()，可以看到第一次修改和第二次修改的差异。

可见，第二次修改确实没有被提交。

如果第二次修改也想提交，如何补救(再次add然后commit)：

```shell
git add GitInro.txt

$ git commit -m "append second changes-git change files"
[master 37f97c0] append second changes-git change files
 1 file changed, 1 insertion(+), 1 deletion(-)
```

`git diff HEAD -- GitInro.txt`确认没有任何差别了，所有change都已经commit。

#### 3.4撤销修改

- [ ] **撤销工作区的修改**

  ​	突然发现在某个文件里写了一句错的离谱的话，比如在GitInro.txt里写了一句“Git像SVN和CVS一样是集中式的版本控制系统”，**幸好还没add**。

  ```shell
  $ cat GitInro.txt
  1.初步认识git
  git 下载目录： https://git-scm.com/download
  
  安装了git后，在gitbash配置git
  $ git config --global user.name "Your Name"
  $ git config --global user.email "email@example.com"
  
  I modify 1st
  
  Git tracks changes.
  
  Git tracks files.
  Git像SVN和CVS一样是集中式的版本控制系统
  ```

  ​	别慌，`git checkout -- <file>`可以丢弃工作区的修改，就回到和版本库一模一样的状态；(可以用git diff检查一下或者直接打开文件看看效果)

  > [新版本的gitbash已经将指令换成了`git restore <file>`，实测是生效的。]()

  ```shell
  $ git checkout -- GitInro.txt
  ```

  ​	如果这是一个刚被add的文件，加了这么一句胡话，那么执行成功后，会回到添加到暂存区后的状态；

  总之，[`git checkout -- <file>`就是让这个文件回到最近一次`git commit`或`git add`时的状态。]()

- [ ] **撤销暂存区的修改**

  ​	继续上面的话题，如果加了这句胡话，没来得及撤销，直接给git add了该怎么办呢？

  [命令`git reset HEAD <file> `可以把暂存区的修改撤销掉]()（unstage），重新放回工作区：

  > [新版本的gitbash已经将指令换成了git restore --staged  <file>，实测是生效的。]()

  ```shell
  $ git reset HEAD GitInro.txt
  Unstaged changes after reset:
  M       GitInro.txt
  ```

  git status查看现状：（暂存区是干净的，工作区有修改）

  ```shell
  $ git status
  On branch master
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
          modified:   GitInro.txt
  
  no changes added to commit (use "git add" and/or "git commit -a")
  ```

  那么就变成到上一个问题了“如何撤销掉工作区的修改”--->执行git checkout --GitIntro.txt吧。

- [ ] **撤销版本库的修改**

  如果不幸git add 了还git commit 了，那么就采用3.1章节的的版本回退吧，git reset指令，`git reset --hard HEAD^`.

  <u>**(上述的撤销修改，这里有个大前提：没有push到远端！！！**)</u>

#### 3.5删除文件

新建一个文件，NewFileToBeDeleted.txt，然后git add、git commit。查看状态如下：

```shell
$ git status
On branch master
nothing to commit, working tree clean
```

现在去文件夹手动删除这个文件，执行`git status`看git如何检测。

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    NewFileToBeDeleted.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

- 如果你不想删除这个文件，系统提示可以用`git restore <file>`撤销工作区做的删除动作；这个和老版本的Gitbash建议的`git checkout -- <file>`是相同作用的。文件恢复到上一次commit的状态，如果在这之间有改动就丢失了。

- 如果你真的想从版本库中删除这个文件，先执行`git rm <file>`，再`git commit`吧，效果如下：

  ```shell
  $ git rm NewFileToBeDeleted.txt
  rm 'NewFileToBeDeleted.txt'
  ```

  继续提交：

  ```shell
  $ git commit -m "remove NewFileToBeDeleted.txt"
  [master 0949808] remove NewFileToBeDeleted.txt
   1 file changed, 1 deletion(-)
   delete mode 100644 NewFileToBeDeleted.txt
  ```

  至此，文件就从版本库中被删除了。

  

### 4.远程仓库

- **Step0**：上[GitHub](http://www.github.com/)注册一个自己的账号，记住自己注册到的用户名、邮箱、密码；

- **Step1**：创建SSH Key。在用户主目录(在GitBash中执行`cd ~`，然后执行`pwd`，就会看到主目录路径了，我的是C:\Users\Benjamin)下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

  ```shell
  $ ssh-keygen -t rsa -C "youremail@example.com"
  ```

  ​	你需要把邮件地址换成你自己的邮件地址，然后**一路敲回车**，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。

  ​	如果一切顺利的话，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

- **Step2**：登陆GitHub，打开“Account settings”，“SSH Keys”页面-->然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的所有内容。下图是我添加成功的SSH-Key。

  ![image-20200315185604950](C:\Users\liwenyao\AppData\Roaming\Typora\typora-user-images\image-20200315185604950.png)

  SSH Key作用：GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议。GitHub允许你添加多个Key，假定你有若干电脑，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

  **[注意：GitHub的东西默认都是公开的，所以个人敏感信息慎重。]()**

  如果不想让别人看到你的Git仓库：一是免费创建个人的私有仓库，(**在2019年1月8日前，是要交钱给Git买服务的，因为2018年微软收购了GitHub**）目前个人免费私有仓库的限制是只能支持3人协作开发；二是自己搭建Git服务器(公司内部开发必备)；

  创建个人的私有仓库，GitHub界面右上角“+”号，“New repository"，如下图：

  ![image-20200315194106621](C:\Users\liwenyao\AppData\Roaming\Typora\typora-user-images\image-20200315194106621.png)

#### 4.1添加远程库

​		按照上一章节新建私有仓库的方法，可以创建一个自己的私有仓库(上图勾public就是公有仓库，自选)。现在的情景是，你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步。

​		我现在已经创建了一个名为**trainGit_private**的私有仓库(**只有一个文件README.md**)。

- 关联本地仓库和远端代码库

  现在需要把本地仓库和远端的进行关联，并且把本地的内容推送到远端GitHub仓库。

  回到本地仓库的目录（如前面章节所述，我这边是E:\Git_Practice\trainGit）,执行如下命令：

  ```shell
  $ git remote add origin git@github.com:kissingfire123/trainGit_private.git
  ```

  ​		注意把`kissingfire123`和trainGit_private换成你自己的GitHub账户名和远端仓库名，不然推送代码是有问题的。（指令执行完没有任何提示就对了，”没有消息就是最好的消息"）。

  ​		添加后，远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。

- 推送本地库的所有内容到远程库上

​		大神的教程中是直接运行`git push -u origin master`，但是我这边失败报错了，看提示的意思是由于本地仓库和远程仓库的版本拥有的文件不一样，哦，我新建的远程仓库有一个文件`README.md`啊！！(打了勾”Initialize this repositorr with a README“)自己的锅自己背，只能先把README.md文件pull下来，再把本地的所有文件push上去。

```shell
$ git push -u origin master
To https://github.com/kissingfire123/trainGit_private.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/kissingfire123/trainGit_private.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

​	 	发现问题所在，[执行 `git pull --rebase origin master`成功把远端独有文件`README.md`给拉取到本地]()：

```shell
$ git pull --rebase origin master
warning: no common commits
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 620 bytes | 5.00 KiB/s, done.
From github.com:kissingfire123/trainGit_private

*branch            master     -> FETCH_HEAD

*[new branch]      master     -> origin/master
First, rewinding head to replay your work on top of it...
Applying: wrote a GitIntro file
Applying: I add 2 more GitIntro files
Applying: add one mark line at last
Applying: make mark of file2
Applying: Add GPL License header file
Applying: Git tracks changes or files
Applying: append second changes-git change files
Applying: changes to GitIntro3
Applying: add a file to be deleted test
Applying: remove NewFileToBeDeleted.txt
```

终于可以放心的git push了，[执行`git push origin master`将本地仓库文件全部推送到远端]()：

```shell
$ git push origin master
Warning: Permanently added the RSA host key for IP address '52.74.223.119' to the list of known hosts.
Enumerating objects: 30, done.
Counting objects: 100% (30/30), done.
Delta compression using up to 4 threads
Compressing objects: 100% (27/27), done.
Writing objects: 100% (29/29), 3.10 KiB | 454.00 KiB/s, done.
Total 29 (delta 11), reused 0 (delta 0)
remote: Resolving deltas: 100% (11/11), done.
To github.com:kissingfire123/trainGit_private.git
   f09c375..db0b8b4  master -> master
```

至此，登陆远端Github，刷新页面，发现本地仓库内容都成功推送到了远端。

#### 4.2从远程库克隆

4.1节我们讲了先有本地库，后有远程库的时候，如何关联远程库。

现在，假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。

远程库就使用上述创建好的**trainGit_private**远程仓库，在本地找一个空文件夹的位置，打开GitBash，我这里是选择了之前本地仓库的旁边=>`/e/Git_Practice/trainGit_ClonePlace`，执行`git clone git@github.com:kissingfire123/trainGit_private.git`将远程仓库所有文件克隆到本地：

```shell
$ git clone git@github.com:kissingfire123/trainGit_private.git
Cloning into 'trainGit_private'...
remote: Enumerating objects: 32, done.
remote: Counting objects: 100% (32/32), done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 32 (delta 11), reused 29 (delta 11), pack-reused 0
Receiving objects: 100% (32/32), done.
Resolving deltas: 100% (11/11), done.
```

使用GitBash验证一下是否将文件全部clone到了本地：

```shell
$ pwd
/e/Git_Practice/trainGit_ClonePlace/trainGit_private
```

```shell
$ ls -a
./  ../  .git/  GitInro.txt  GitInro2.txt  GitInro3.txt  GPL_HeadContent.txt  README.md
```

至此，远程仓库的所有内容都克隆到本地文件夹了。

小结：*要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。Git支持多种协议，包括`https`，但通过`ssh`支持的原生`git`协议速度最快。*

### 5.分支管理

> 分之的意义：你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

#### 5.1创建与与合并分支

一开始的时候，`master`分支是一条线，Git用`master`指向最新的提交，再用`HEAD`指向`master`，就能确定当前分支，以及当前分支的提交点：

![git-br-initial](https://www.liaoxuefeng.com/files/attachments/919022325462368/0)

每次提交，`master`分支都会向前移动一步，这样，随着你不断提交，`master`分支的线也越来越长。

当我们创建新的分支，例如`dev`时，Git新建了一个指针叫`dev`，指向`master`相同的提交，再把`HEAD`指向`dev`，就表示当前分支在`dev`上：

![git-br-create](https://www.liaoxuefeng.com/files/attachments/919022363210080/l)

可以发现，Git创建一个分支很快，因为除了增加一个`dev`指针，改改`HEAD`的指向，工作区的文件都没有任何变化！

不过，从现在开始，对工作区的修改和提交就是针对`dev`分支了，比如新提交一次后，`dev`指针往前移动一步，而`master`指针不变：

![git-br-dev-fd](https://www.liaoxuefeng.com/files/attachments/919022387118368/l)

假如我们在`dev`上的工作完成了，就可以把`dev`合并到`master`上。Git怎么合并呢？最简单的方法，就是直接把`master`指向`dev`的当前提交，就完成了合并：

![git-br-ff-merge](https://www.liaoxuefeng.com/files/attachments/919022412005504/0)

所以Git合并分支也很快！就改改指针，工作区内容也不变！

合并完分支后，甚至可以删除`dev`分支。删除`dev`分支就是把`dev`指针给删掉，删掉后，我们就剩下了一条`master`分支：

![git-br-rm](https://www.liaoxuefeng.com/files/attachments/919022479428512/0)

上面是理论基础（不得不说廖雪峰老师的图和教程实在是太好了，言简意赅，说道点子上），下面开始实战：

首先，我们创建`dev`分支，然后切换到`dev`分支：

```shell
$ git checkout -b dev
Switched to a new branch 'dev'
```

[`git checkout`命令加上`-b`参数表示创建并切换]()，相当于以下两条命令：

```shell
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```

然后，用`git branch`命令查看当前分支：

```shell
$ git branch
* dev
  master
```

[`git branch`命令会列出所有分支]()，当前分支前面会标一个`*`号。

然后，我们就可以在`dev`分支上正常提交，比如对`README.md`做个修改，最末尾加上一行"Creating a new branch named 'dev' is quick."：

```shell
$ git add README.md

$ git commit -m "new a branch test"
[dev eb1730d] new a branch test
 1 file changed, 2 insertions(+)
```

现在切换回master分支，看下刚才的改动是否还在，**显然，刚才添加的内容没有了**。

因为那个提交是在`dev`分支上，而`master`分支此刻的提交点并没有变:

```shell
$ git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
```

```shell
$ cat README.md
# trainGit_private
trainingGit_myself_2020_0315
```

![git-br-on-master](https://www.liaoxuefeng.com/files/attachments/919022533080576/0)

现在，我们把`dev`分支的工作成果[执行`git merge dev`把dev分支合并到`master`分支上：]()

```shell
$ git merge dev
Updating db0b8b4..eb1730d
Fast-forward
 README.md | 2 ++
 1 file changed, 2 insertions(+)
```

分之合并成功之后，再看当前master分支下的`README.md`文件内容，成功修改：

```shell
$ cat README.md
# trainGit_private
trainingGit_myself_2020_0315

Creating a new branch named 'dev' is quick.
```

注意到上面merge过程的`Fast-forward`信息，Git告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。不是每次合并都能`Fast-forward`，我们后面会讲其他方式的合并。

合并完成后，就可以放心地[执行 `git branch -d dev`删除`dev`分支了]()：

```shell
$ git branch -d dev
Deleted branch dev (was eb1730d).
```

删除后，查看`branch`，就只剩下`master`分支了：

```shell
$ git branch
* master
```

因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。

> **注意**：`git checkout -b <branch>`有等效指令`git switch -c <branch>` ;
>
> ​			`git checkout  <branch>`有等效指令`git switch   <branch>` ;			   

#### 5.2解决冲突