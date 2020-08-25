# git

## 创建仓库
git命令必须在仓库目录内执行
1. 创建一个空目录并进入
```
$ mkdir learngit
$ cd learngit
```
2. 将这个目录变成git可以管理的仓库
```
$ git init
```

3. 将一个文件**添加**到仓库
```
$ git add readme.txt
```
注意：添加某个文件时，该文件必须在当前目录下存在

4. 将这个文件**提交**到仓库
```
$ git commit -m "wrote a readme file"
```
-m后面是本次提交的说明


## 对仓库中内容进行修改
用以下命令可以掌握工作区的状态，它会告诉我们文件有没有修改过
```
git status
```
而以下命令可以查看修改的内容
```
git diff readme.txt
```
### 1.版本回退

我们在工作中会不断地对文件进行修改，当我们觉得修改到一定程度的时候，就可以进行一次commit，相当于打游戏时进行一次存档。一旦文件被该乱了，或者不小心删除了文件，还可以从最近的一个commit中恢复。

以下命令告诉我们commit的历史记录,它会显示从最近到最远的提交版本
```
git log
```

在Git中，**用HEAD表示当前版本**，也就是最新的提交，上一个版本就是HEAD^，上上一个版本就是HEAD^^，往上100个版本就是HEAD~100。如果我们要把当前版本回退到上一个版本，就可以用如下的命令
```
git reset --hard HEAD^
```

显然，当回退到上一个版本之后，刚才的当前版本已经不会在git log中看到了。但扔可以通过commit id回到刚才的那个版本（7c0b9b为那个版本git版本号的前几位）
```
git reset --hard  7c0b9b
```
但是那个版本的版本号找不到或者不记得了怎么办？不要紧，git记录了我们的每一次命令,通过如下命令行查看命令历史，就可以找到我们想重返的版本的id
```
$ git reflog
7c0b9b5 (HEAD -> master) HEAD@{0}: reset: moving to 7c0b9b
06d21e9 HEAD@{1}: reset: moving to HEAD^
7c0b9b5 (HEAD -> master) HEAD@{2}: commit: add a line
06d21e9 HEAD@{3}: commit (initial): write a readme text
```

### 2.工作区和暂存区
Git管理的文件分为工作区和版本库(.git隐藏目录)。版本库又分为暂存区stage和git为我们创建的第一个分支master。

我们把文件添加到git版本库分为两步：
* 用git add把文件修改添加到暂存区
* 用git commit将暂存区的内容添加到当前分支

可以理解为：将需要提交的文件修改先全都放到暂存区，然后，一次性提交暂存区的所有修改。

### 3.撤销修改和删除文件
如下命令可以用来撤销对工作区的修改
```
$ git checkout -- readme.txt
```
有两种情况：
* readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
* readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，以上命令就是让这个文件工作区回到最近一次git commit或git add时的状态。

但是如果对工作区修改后还add到了暂存区该怎么办呢？

1. 先用如下命令可以撤销对暂存区的修改（unstage），然后重新放回工作区
```
$ git reset HEAD readme.txt
```
2. 现在暂存区干净了，但是工作区还有修改，接下来再撤销掉工作区的修改：
```
$ git checkout -- readme.txt
```

总结撤销修改的三种情况：
* 当改乱了文件的内容并且还未add，想直接丢弃工作区的修改时，用命令git checkout -- file
* 当改乱了文件的内容并且add到了暂存区时，想丢弃修改，第一步先用命令git reset HEAD file丢弃暂存区的修改，第二步再用命令git checkout -- file丢弃工作区的修改。
* 当已经把改乱的文件commit到版本库时，想撤销提交，这时就需要版本回退。

总结删除文件的两种情况：
当我们把工作区的一个文件删除时：
* 如果我们确实要从版本库中删除该文件，那么就用git rm file删除掉，并且git commit
* 如果我们其实是不小心删错了，那就可以用git checkout -- file将误删的文件恢复到最新版本。
  
注意：git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。



## 远程仓库
Github用来提供Git仓库托管服务，它相当于一个服务器，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。

1. 在github中添加本地计算机的SSH Key。这样GitHub就可以识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。
2. 在github中建立一个 repository。并在本地库中运行如下命令将本地版本库和github上的远程库关联起来（远程库的名字就叫origin）
```
$ git remote add origin https://github.com/wyh317/learnGit.git
```
3. 将本地库的所有内容推送到远程库上,实际上是把当前分支master推送到远程。并且本地的master分支和远程的master分支关联了起来
```
$ git push -u origin master
```
由于远程库是空的，所以第一次推送master分支时，带了"-u"。之后，只要本地做了提交，就可以通过如下命令将本地的master分支的最近修改推送至Github
```
$ git push origin master
```

可以使用如下命令从github上克隆出一个本地库
```
$ git clone git@github.com:wyh317/learnGit.git
```
当从远程仓库克隆时，Git自动把本地的master分支和远程的master分支对应起来了，并且远程仓库的默认名称是origin。可以用git remote或者git remote -v查看远程库的信息。

## 分支管理
Git鼓励使用分支完成某个任务，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但过程更安全。

* 查看分支：git branch
* 创建分支：git branch name
* 创建一个分支并切换到它：git switch -c name
* 切换到某个分支：git switch name
* 合并某分支到当前分支：git merge name
* 删除分支：git branch -d name

当两个分支的文件修改有冲突时，git无法完成自动合并，我们必须先手动把冲突解决然后提交，再合并。用git log --graph命令可以看到分支合并图。

通常在合并分支时，git会默认使用fast forward模式，但这种模式下删除分支后，会丢掉分支信息。如果使用以下命令行来merge(即将分支test合并到当前分支）：
```
$ git merge --no-ff -m "merge with no-ff" test
```
那么Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。（因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去），能看出来做过合并，而fast forward合并后就看不出来曾经做过合并

***分支策略***：
* master分支应该是非常稳定的，仅用来发布版本，不能在上面工作
* dev分支是不稳定的，用来工作用，到版本发布时，再把dev分支合并到master上
* 团队的每个成员都在dev的分支上工作，每个人有自己的分支，工作完往dev上合并即可。

***Bug分支***：  
在修复bug时，我们通过创建新的bug分支来进行修复，修复完之后进行合并，最后将这个bug分支删除。

当手头上的工作还没有完成时，我们可以用git stash命令将当前工作现场“储藏”起来，然后去修复bug，修复后再用git stash pop命令恢复现场继续工作。

在master分支上修复的bug，想要合并到当前dev分支，可以用如下命令，把修复bug的commit（用其id表示）“复制”到当前分支，避免重复劳动。
```
git cherry-pick commitid
```

***Feature分支***：  
每添加一个新功能，最好新建一个feature分支并在上面开发，完成后与主分支进行merge，最后删除该feature分支。

***多人协作***：  
* 首先，用git push origin <branch-name>向远程推送自己的更改
* 如果推送失败，说明队友的最新提交和我们正要试图的提交有冲突，那我们先用git pull抓取远程的新提交
* 在本地解决冲突，并在本地提交
* 将冲突解决后的分支提交到远程git push origin <branch-name>

如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。


## 标签管理
发布一个版本时，我们通常会在版本库中打一个标签（tag），一个标签对应着一个commit，例如id为6a5819e的commit对应着标签为v1.2的版本，这样便与日后查找。

可以通过如下的命令进行打标签：
```
$ git tag v1.3
$ git tag v1.2 6a5819e
```
第一行将标签v1.3打到当前分支最新提交的commit上，第二行将标签v1.2打到id为6a5819e的commit上。

命令git tag可以查看所有标签。git show <tagname>可以看到标签的说明文字。

删除一个本地标签：
```
$ git tag -d v0.1
```
向远程推送一个本地标签：
```
$ git push origin <tagname>
```
向远程推送全部未推送过的本地标签:
```
$ git push origin --tags
```
删除一个远程标签：
```
$ git push origin :refs/tags/<tagname>
```