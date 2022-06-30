# git note

### 什么是git

* 最先进的分布式**版本控制系统**

* 从版本控制我们就可以了解到基本功能，做一个简单的叙述：我们用Microsoft Word写一篇较长的文章，避免不了的是经常修改。很可能会面临一种情况：我们删除的一部分在日后我们发现不应该删除，或者你想知道每一次修改的是什么内容

* 而git可以做到这样的记录：

* | 版本 | 文件名 | 用户  | 说明             | 日期 |
  | ---- | ------ | ----- | ---------------- | ---- |
  | 1    | a.out  | Lynn  | 删除了服务条款5  | 时间 |
  | 2    | a.out  | iYan  | 增加了限制人数   | 时间 |
  | 3    | a.out  | KLein | 调整了资金变动   | 时间 |
  | 4    | a.out  | Lynn  | 调整免费升级周期 | 时间 |

* git 是用C语言开发的

### 解释集中式和分布式

* 我们提到了git是最先进的**分布式**版本控制系统。与分布式类似的概念还有集中式：
* 集中式：版本库（就是类似于上面的表格的数据）集中存放在中央服务器。我们工作的时候都需要从中央服务器取得最新的版本，然后再工作。这就涉及到一个问题：联网。
* 分布式：不存在中央服务器，每个电脑上都有一个完整的版本库。分布式版本控制系统的安全性要高很多，因为**每个人的电脑里都有一个完整的版本库**

### 创建版本库

* 版本库又叫仓库（repository），相当于一个目录，这个目录下的所有文件都可以呗git管理起来，每个文件的删除、修改，git都能跟踪

```
mkdir learngit  // 创建一个名为learngit的文件夹
cd learngit     //改变工作目录到learngit
pwd             //输出当前工作路径

git init             //把这个目录编程Git可以管理的仓库
然后编写一个文本文件：readme.txt 内容随意
git add           //把文件添加到仓库
git commit -m “comment”  //把文件提交到仓库 添加和提交的区别后面会提到

```

* 需要注意：
* * git add 	file1.txt	file2.txt 可以用这种方式同时添加两个文件
  * git 操作要在指定的工作目录执行

### 版本回溯

假如最初的readme.txt文件内容如下：

```
Git is a version control system.
Git is free software.
```

现在改为：

```
Git is a distributed control system.
Git is free software.
```

现在我们执行命令：

```
git status    //就是得到当前目录的状态
```

我们会得到提示：

```
我们会得到 modified 提示
```

意为某个文件被修改了,我们执行以下命令：

```
git diff readme.txt  //顾名思义，就是查看readme.txt 这个文件的变化
```

可以查看变化的内容,如果确定变化没有问题，是我们修改的内容，就可以提交

```
git commit -m "add distributed" // 把更改的提交到仓库
```

这个时候再查看目录状态：

```
git status 
得到：
On branch master
nothing to commit, working tree clean
```

意为工作目录是干净的

将 readme.md 改为：

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

再重复以上操作；

我们现在有三个版本的readme.txt:

version 1:

```
Git is a version control system.
Git is free software.
```

version 2:

```
Git is a distributed version control system.
Git is free software.
```

version 3:

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

我们用git就是为了让他记录我们的历史版本：

```
git log
```

* 如果我们想要回溯到add distributed的版本该怎么做？

```
git reset --hard HEAD     //hard参数的意义后面会涉及
```

现在我们回到了上一个版本，如果再使用git log 查看日志，我们会发现”append GPL“ 的版本不见了，但是我们仍然可以回到**未来**的版本。

我们第一次使用git log命令的时候每个版本都有一个版本号码，是SHA算法得到的数，只要我们找到对应的版本的版本号（取前几位即可）就能回到相应的版本：

```
git reset --hard xxxxx
```

* 存在一个问题，如果我们关掉了命令窗口，没办法看到我们第一次使用git log命令的结果，我们该怎么做呢？

```
git reflog    //这个命令可以展示处你执行的历史命令，再执行结果中可以看到对应的版本号，然后回溯即可
```

---

* git reset --hard commit_id
* git log
* git reflog

### 工作区和暂存区

<img src="C:\Users\17632\AppData\Roaming\Typora\typora-user-images\image-20220624173441000.png" alt="image-20220624173441000" style="zoom:50%;" />

* 前面提到使用add是添加文件，而commit是提交；这两个分别对应的位置就是**暂存区**和**分支**

实践：

对readme.txt再次进行修改：

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
```

现在我们创建一个新的文件，内容是什么无所谓：

```
touch LICENSE
```

执行命令

```
git status
```

会有两类提示：modified以及untracked files，分别为修改过的文件和没有添加过的文件

现在执行两次git add将两个文件添加到暂存区，然后commit一次提交：

<img src="C:\Users\17632\AppData\Roaming\Typora\typora-user-images\image-20220624175144741.png" alt="image-20220624175144741" style="zoom:50%;" />

---

现在我们进行一系列操作加深对他的理解以及明白Git追踪并管理的是**修改**：

1、对readme.txt 进行修改：

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes.
```

2、添加（到缓存区）

```
git status readme.txt
git status
```

3、再次修改readme.txt:

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
```

4、提交（到分支）

```
git commit -m "git tracks changes"
git status 
```

在执行git status之后，会有提示modified ： readme.md，意为这个文件有修改，也就是第二次没有被的修改没有被提交

我们可以看一下我们操作的顺序，可以得知第二次修改没有被添加到暂存区，只有第一次修改被添加到了暂存区，因此commit参数只会把暂存区中的内容放到分支；解决方法就是再使用一次add 和 commit 参数；

```
git diff HEAD -- filename
```

这个命令可以查看工作区和版本库最新版本的区别



### 撤销修改

如果你发现在readme.txt 中有一个错误，多加了一行内容：

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
I can't choice 
```

我们使用以下命令之后出现提示：

```
git status


use "git restore <file>..." to discard changes in working directory
```

这就是在工作区撤销修改的命令：

```
git restore <filename>
```

其实这是一种情况：

* 修改内容还在工作区，没有放到暂存区
* 另一种情况是已经放到暂存区

如果已经放到缓存区，可以用：

```
git restore --stage <filename>
```

### 删除文件

先创建一个测试文件，然后添加、提交

```
touch test
git add test
git commit -m "add a new file"
```

删除的方法是：

```
rm test           //从工作区删除
git rm test       //从版本库删除
```

如果误删了工作区的文件，可以用上面撤销修改的命令：

```
git restore <file>
```

用来撤销工作区的修改（将工作去恢复成分支的状态），对应的：

```
git restore --stege <filename>
```

是撤销暂存区的修改（见暂存区恢复成分支的状态）



### 远程仓库

* Github从名字就可以知道是提供Git仓库托管服务的
* 只要注册一个Github账号，就可以免费获得Git远程仓库

步骤1：

```
ssh-keygen -t rsa -C "youremail@example.com"
```

需要做的上就是将email换成自己的邮箱，然后一路回车。

这个时候会有提示在某个地址生成了两个文件：公钥和私钥

步骤2：

登录Github，打开Settings -> ssh and gpg key  -> new ssh key 

将公钥内容（.pub后缀的文件）复制到Key下面



### 添加远程库

* Github上的既可以作为一个备份又可以让其他人通过仓库来协作
* 点击 Your repository，然后new，按步骤来就可以

```
git remote add origin git@github.com:yourname/dir.git
```

注意：这一命令需要在工作目录中执行；还有命令中的yourname以及dir都是根据自己的github来进行输入的内容



* 下一步就可以将本地库推送到远程库：

```
git push -u origin master
```

由于远程库是空的第一次推送到master分支时候，加上了-u的参数，Git不但会把本地的master分支内容推送到远程新的master分支，还会把本地的master分支于本地的master分支联系起来。

从现在起，本地做了提交，就可以通过命令：

```
git push origin master
```

#### ssh警告

在第一次使用Git的clone或者push命令的时候，会有警告，输入yes回车即可

#### 删除远程库

```
git remote -v
git remote rm origin
```

删除的意思是解除绑定关系，而不是物理上删除远程库，远程库本身没有任何改动

---

小结：

关联一个远程库：

```
git remote add origin git@github.com:path/repo_name.git
```

其中origin是远程库的默认习惯命名

第一次推送要加参数u

```
git push -u origin master
```

之后就可以不加了：

```
git push origin master
```

### 从远程库克隆

克隆主要命令是：

```
git clone git@github@qq.com:path/repo_name.git
```

然后在本地就会生成一个与repo_name同名的目录





### 分支管理

* 版本回溯中，每次提交git都会将其串成一条时间线，这条时间线就是一条分支。截止到目前，只有一条时间线，在Git里，这叫主分支，也就是master分支
* HEAD严格来说不是指向提交，而是指向master，master才是只想提交的

实战：

* 首先创建dev分支，然后切换到dev分支

```
git checkout -b dev
```

* 可以用以下命令代替：

```
git branch dev
git checkout dev
```

现在我们对readme.txt 进行一定的修改然后提交

```
git checkout master //切换到主分支
cat readme.txt       //浏览readme文件的内容
```

这个时候我们发现我们的修改“消失了”，是因为dev超前于master分支，我们只需要将master分支与dev分支合并就可以；

```
git merge dev 
```

合并完之后就可以看到修改的内容了，这个时候就可以将dev分支删除

```
git branch -d dev
git branch
```

 #### switch分支

创建并切换到分支：

```
git switch -c dev
```

切换到已有分支：

```
git switch master
```

---

命令总结

查看分支：

```
git branch
```

创建分支：

```
git branch <name>
```

切换分支：

```
git checkout <name>   or   git switch <name>
```

创建加切换分支

```
git checkout -c <name>    or    git switch -c <name>
```

合并（某）分支（到当前分支）：

```
git merge <name>
```

删除分支：

```
git branch -d <name>
```



### 解决冲突

实验：

步骤1：

```
git switch -c feature1    //创建一个分支
```

步骤2：修改readme.txt最后一行内容，比如：

```
Creating a new branch is quick AND simple
```

步骤3：在feature1分支上提交：

```
git add readme.txt;git commit -m "AND simple"
```

步骤4：切换到master分支：

```
git switch master  //这个时候git提示我们当前master分支比远程的master超前一个提交
```

步骤5：在master分支上将最后一行改为：

```
Creating a new branch is quick & simple
git add readme.txt ;git commit -m "& simple"  //提交
```

步骤6：合并

```
git merge feature1
```

然后发生冲突，解决冲突：

```
vim readme.md
```

将`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容，将冲突修改，然后重新提交。

用参数

```
git log //查看合并情况
```

然后将分支删除

```
git branch -d feature1
```

工作结束



### 分支管理策略

* 通常合并分支Git会使用Fast forward模式，这种模式下，删除分支后，会丢掉分支信息；
* 如果要强制禁用Fast Forward模式，**Git就会在merge时生成一个新的commit**

实验：

步骤1、创建并且切换dev分支

```
git switch -c dev
```

步骤2、修改readme.md 文件，提交一个新的commit

```
git add readme.txt
git commit -m "add merge"
```

步骤3、切换回master并且合并dev分支，用 --no-ff参数，表示禁用Fast Forward

```
git merge --no-ff -m "merge with no-ff" dev
```

* 因为这次合并要创建一个新的commit，所以加上-m，把commit描述写进去

#### 分支策略

master分支是非常稳定的，也就是仅用来发布新版本，平时工作就在dev分支上



### Bug分支

软件开发中，bug会经常出现，我们可以借助分支，每个bug可以通过一个新的临时分支来修复，修复后开始合并，然后将临时分支删除

* 情景：你现在在dev分支工作，但是master有一个bug需要去修复，你现在在dev上的工作才进行到一半，还不想提交，这个时候可以用命令：

```
git stash       //将工作现场存储起来
```

然后切换到master分支上：

```
git switch master    //切换分支
git switch -c issue_101    //创建修复bug的分支
fix bug
git add readme.txt
git commit -m "fix bug 101"   //执行完这个指令会返回commit编号
git switch master              //切换到master分支
git merge -no-ff -m "merge bug fix 101" issue_101
```

问题：既然master分支上有问题，那么dev分支上应该也会有一模一样的bug，理论上只要重新执行修复bug的做法就可以，但是有其他边界的方法

* 复制上面修复bug提交命令返回的commit编号

```
git cherry-pick commit_code
```

* 注意：在执行前要进行

```
git stash //保护现场操作
```

---

修复完bug之后，如何从保护现场恢复呢？

* ```
  git stash apply //stash内容不删除
  ```

* ```
  git stash pop   //stash内容会被删除
  ```

可以用：

```
git stash list    //查看stash状态内容
```



### Feature分支

软件开发中，总会有很多功能需要添加，但是不希望因为一些实验性质的代码把主分支搞乱，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后进行合并，最后删除分支。



实验：开发代号为Vulcan的新功能，该功能计划用于下一代星际飞船。

```
git switch -c feature-vulcan
git add vulcan.py

git status
git commit -m "add feature vulcan"

git switch dev     //返回dev准备合并
```

就在此时，接到上级命令，因经费不足，新功能必须取消！

```
git branch -d feature-vulcan
```

Git提示，没有合并，如果删除将丢失修改，如果要强制删除可以用 -D



### 多人协作

当从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称时origin

查看远程库的信息：

```
git remote
```

用下述命令查看更详细的信息：

```
git remote -v
```

推送分支：

```
git push origin master
```

如果要推送到其他分支：

```
git push origin dev
```



#### 抓取分支

多人协作的时候，大家都会往master和dev分支上推送各自的修改

```
git clone git@github.com:path/repo_name.git
```

可以用上述命令获取到对应仓库的内容，当我们clone的时候默认情况只能看到master分支，可以用下面的命令查看

```
git branch
git checkout -b dev origin/dev     //创建远程origin的dev分支到本地
```

如果有人进行了提交，而恰巧我们也对文件做了修改，并试图推送，结果会报错

我们需要从远端用

```
git pull
```

从origin/dev上抓下来，然后在本地合并，解决冲突在合并

```
git branch --set-upstream-to=origin/dev dev
```

上述时git pull失败的解决方法

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令

`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。



### 标签管理

#### 创建标签

Git中打标签的方法：`git branch`查看分支，然后用`git switch master`切换分支。然后用命令`git tag <name>`就可以打标签

查看标签的命令：`git tag`

默认标签是打在最新的commit上的，但是如果有一次commit忘记打标签怎么办？

方法是找到历史提交的commit id就可以了：

```
git tag v1.0 commit_id
```

还可以创建带说明的标签：

```
git tag -a version_id -m "version formation" commit_id 
```

可以用`git show <tagname>`

#### 操作标签

可以删除标签：

```
git tag -d <tagname>
```

要推送某个标签到远程，使用命令：

```
git push origin <tagname>
```

或者一次性推送全部尚未推送到远程的本地标签 

```
git push origin --tags
```

如果标签已经推送到远程，要删除就需要先删除门第标签：

```
git tag -d <tagname>
```

然后从远程删除：

```
git push origin :refs/tags/<tagname>
```

 