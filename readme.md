Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
一、时光机穿梭
git status命令看看结果
$ git diff readme.txt 
git diff顾名思义就是查看difference，显示的格式正是Unix通用的diff格式
提交修改和提交新文件是一样的两步，第一步是git add：
$ git add readme.txt
第二步git commit
$ git commit -m "add distributed"

1、版本回退
git log命令查看历史记录，显示从最近到最远的提交日志
$ git log
如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline参数：
$ git log --pretty=oneline
在Git中，用HEAD表示当前版本，也就是最新的提交1094adb...（注意我的提交ID和你的肯定不一样），
上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
$ git reset --hard HEAD^
$ cat readme.txt
$ git reset --hard commit id
commit id版本号没必要写全，前几位就可以了，Git会自动去找
$ git reflog
用来记录你的每一次命令及其commit id

2、工作区（Working Directory）
工作区就是你在电脑里能看到的目录，比如learngit文件夹就是一个工作区。

3、暂存区（Repository）
工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。
Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，
还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

4、管理修改

5、撤销修改
git checkout -- file可以丢弃工作区的修改
$ git checkout -- readme.txt
git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到git checkout命令。

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

6、删除文件
确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit：
$ git rm test.txt
$ git commit -m "remove test.txt"

$ git checkout -- test.txt
git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。


二、远程仓库
1、创建SSH Key：
$ ssh-keygen -t rsa -C "youremail@example.com"
在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，
id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
2、添加远程库
（1）登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库。
（2）在本地的仓库下运行命令：
$ git remote add origin git@github.com:michaelliao/learngit.git
请千万注意，把上面的michaelliao替换成你自己的GitHub账户名。
（3）把本地库的所有内容推送到远程库上：
$ git push -u origin master
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master
分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，
在以后的推送或者拉取时就可以简化命令。
$ git push origin master
（4）删除远程库
如果添加的时候地址写错了，或者就是想删除远程库，可以用git remote rm <name>命令。
使用前，建议先用git remote -v查看远程库信息：
$ git remote -v
origin  git@github.com:michaelliao/learn-git.git (fetch)
origin  git@github.com:michaelliao/learn-git.git (push)
然后，根据名字删除，比如删除origin：
$ git remote rm origin
此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。
要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。
【小结】
要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；
关联一个远程库时必须给远程库指定一个名字，origin是默认习惯命名；
关联后，使用命令git push -u origin master第一次推送master分支的所有内容；
此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

3、从远程库克隆
首先，登陆GitHub，创建一个新的仓库，名字叫gitskills：
现在，远程库已经准备好了，下一步是用命令git clone克隆一个本地库（找到合适的目录克隆）：
$ git clone git@github.com:lvdelin/gitskills.git
注意：把Git库的地址换成你自己的，然后进入gitskills目录看看，已经有README.md文件了：
$ cd gitskills
$ ls
README.md
如果有多个人协作开发，那么每个人各自从远程克隆一份就可以了。
Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。
使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。
【小结】
要克隆一个仓库，首先必须知道仓库的地址，然后使用git clone命令克隆。
Git支持多种协议，包括https，但ssh协议速度最快。

三、分支管理
Git的分支是与众不同的，无论创建、切换和删除分支，Git在1秒钟之内就能完成！无论你的版本库是1个文件还是1万个文件。
1、创建与合并分支
Git创建一个分支很快，因为除了增加一个dev指针，改改HEAD的指向，工作区的文件都没有任何变化！
Git合并分支也很快！就改改指针，工作区内容也不变！
删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支。
首先，我们创建dev分支，然后切换到dev分支：
$ git checkout -b dev
Switched to a new branch 'dev'
git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
然后，用git branch命令查看当前分支：
$ git branch
* dev
  master
git branch命令会列出所有分支，当前分支前面会标一个*号。
切换回master分支（有时候master分支名称叫main）：
$ git checkout master
Switched to branch 'master'
dev分支上进行工作，并对文件修改后，工作成果可以合并到master分支上。
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
 git merge命令用于合并指定分支到当前分支。合并后，再查看readme.txt的内容，就可以看到，和dev分支的最新提交是完全一样的。
 Fast-forward信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。
 合并完成后，就可以放心地删除dev分支了：
 $ git branch -d dev
 Deleted branch dev (was b17d20e).
 删除后，查看branch，就只剩下master分支了：
 $ git branch
* master

【switch】
最新版本的Git提供了新的git switch命令来切换分支：
创建并切换到新的dev分支，可以使用：
$ git switch -c dev
直接切换到已有的master分支，可以使用：
$ git switch master

【小结】
Git鼓励大量使用分支：
查看分支：git branch
创建分支：git branch <name>
切换分支：git checkout <name>或者git switch <name>
创建+切换分支：git checkout -b <name>或者git switch -c <name>
合并某分支到当前分支：git merge <name>
删除分支：git branch -d <name>

2、解决冲突
master分支和feature1分支各自都分别有新的提交,造成冲突。
这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突，我们试试看：
$ git merge feature1
Auto-merging readme.md
CONFLICT (content): Merge conflict in readme.md
Automatic merge failed; fix conflicts and then commit the result.
git status也可以告诉我们冲突的文件：
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   readme.md

no changes added to commit (use "git add" and/or "git commit -a")
我们可以直接查看readme.md的内容：
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1

Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容。
用带参数的git log也可以看到分支的合并情况：
$ git log --graph --pretty=oneline --abbrev-commit
删除feature1分支：
$ git branch -d feature1
【小结】
当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。
用git log --graph命令可以看到分支合并图。

3、分支管理策略
合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。
如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
下面我们实战一下--no-ff方式的git merge：
$ git merge --no-ff -m "merge with no-ff" dev
因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去。
合并后，我们用git log看看分支历史：
$ git log --graph --pretty=oneline --abbrev-commit

在实际开发中，我们应该按照几个基本原则进行分支管理：
首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，
再把dev分支合并到master上，在master分支发布1.0版本；
你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。
【小结】
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，
而fast forward合并就看不出来曾经做过合并。

4、BUG分支
有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，
修复后，合并分支，然后将临时分支删除。
当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支issue-101来修复它，但是，等等，
当前正在dev上进行的工作还没有提交。并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。
但是，必须在两个小时内修复该bug，怎么办？
幸好，Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
现在，用git status查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。
首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支：
$ git checkout master
$ git checkout -b issue-101
现在修复bug，需要把“Git is free software ...”改为“Git is a free software ...”，然后提交：
$ git add readme.txt 
$ git commit -m "fix bug 101"
[issue-101 4c805e2] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
修复完成后，切换到master分支，并完成合并，最后删除issue-101分支：
$ git switch master
$ git merge --no-ff -m "merged bug fix 101" issue-101
太棒了，原计划两个小时的bug修复只花了5分钟！现在，是时候接着回到dev分支干活了！
$ git checkout dev
$ git status
工作区是干净的，刚才的工作现场存到哪去了？用git stash list命令看看：
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：
一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；
另一种方式是用git stash pop，恢复的同时把stash内容也删了：
$ git stash pop
再用git stash list查看，就看不到任何stash内容了：
$ git stash list
你可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：
$ git stash apply stash@{0}

在master分支上修复了bug后，我们要想一想，dev分支是早期从master分支分出来的，
所以，这个bug其实在当前dev分支上也存在。那怎么在dev分支上修复同样的bug？重复操作一次，提交不就行了？
有木有更简单的方法？
同样的bug，要在dev上修复，我们只需要把4c805e2 fix bug 101这个提交所做的修改“复制”到dev分支。
注意：我们只想复制4c805e2 fix bug 101这个提交所做的修改，并不是把整个master分支merge过来。
为了方便操作，Git专门提供了一个cherry-pick命令，让我们能复制一个特定的提交到当前分支：
$ git branch
* dev
  master
$ git cherry-pick 4c805e2
[master 1d4b803] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
【小结】
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场；
在master分支上修复的bug，想要合并到当前dev分支，可以用git cherry-pick <commit>命令，
把bug提交的修改“复制”到当前分支，避免重复劳动。

5、Feature分支
添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，
最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。
删除分支
$ git branch -d feature-vulcan
强行删除分支，需要使用大写的-D参数。
$ git branch -D feature-vulcan
【小结】
开发一个新feature，最好新建一个分支；
如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。

6、多人协作
查看远程库的信息，用git remote：
$ git remote
origin
或者，用git remote -v显示更详细的信息：
$ git remote -v
origin  git@github.com:lvdelin/learngit.git (fetch)
origin  git@github.com:lvdelin/learngit.git (push)
（1）推送分支
推送分支，就是把该分支上的所有本地提交推送到远程库。
推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：
$ git push origin master
如果要推送其他分支，比如dev，就改成：
$ git push origin dev
但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？
master分支是主分支，因此要时刻与远程同步；
dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
（2）抓取分支
多人协作时，大家都会往master和dev分支上推送各自的修改。
$ git pull
设置dev和origin/dev的链接：
$ git branch --set-upstream-to=origin/dev dev

【小结】
查看远程库信息，使用git remote -v；
本地新建的分支如果不推送到远程，对其他人就是不可见的；
从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。

7、Rebase
Git有一种称为rebase的操作，有人把它翻译成“变基”。
rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。

【小结】
rebase操作可以把本地未push的分叉提交历史整理成直线；
rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

四、标签（tag）管理
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。
将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。
Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），
所以，创建和删除标签都是瞬间完成的。
tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。

1、创建标签
在Git中打标签非常简单，首先，切换到需要打标签的分支上：
$ git branch
$ git checkout master
然后，敲命令git tag <name>就可以打一个新标签：
$ git tag v1.0
可以用命令git tag查看所有标签：
$ git tag
默认标签是打在最新提交的commit上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？
方法是找到历史提交的commit id，然后打上就可以了：
$ git log --pretty=oneline --abbrev-commit
$ git tag v0.9 f52c633
注意，标签不是按时间顺序列出，而是按字母排序的。可以用git show <tagname>查看标签信息：
$ git show v0.9

还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：
$ git tag -a v0.1 -m "version 0.1 released" 1094adb

注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。 

【小结】
命令git tag <tagname>用于新建一个标签，默认为HEAD，也可以指定一个commit id；
命令git tag -a <tagname> -m "blablabla..."可以指定标签信息；
命令git tag可以查看所有标签；
命令git show <tagname>查看tag标签信息。

2、操作标签
如果标签打错了，也可以删除：
$ git tag -d v0.1
因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。
如果要推送某个标签到远程，使用命令git push origin <tagname>：
$ git push origin v1.0
或者，一次性推送全部尚未推送到远程的本地标签：
$ git push origin --tags
如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：
$ git tag -d v0.9
然后，从远程删除。删除命令也是push，但是格式如下：
$ git push origin :refs/tags/v0.9
要看看是否真的从远程库删除了标签，可以登陆GitHub查看。

【小结】
命令git push origin <tagname>可以推送一个本地标签到远程仓库；
命令git push origin --tags可以推送全部未推送过的本地标签到远程仓库；
命令git tag -d <tagname>可以删除一个本地标签；
命令git push origin :refs/tags/<tagname>可以删除一个远程标签。

五、自定义Git
1、忽略特殊文件
新建一个.gitignore文件。需要在文本编辑器里“保存”或者“另存为”就可以把文件保存为.gitignore了。
然后把要忽略的文件名填进去，Git就会自动忽略这些文件。
如：
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Java:
*.class

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa

被忽略的文件可以用-f强制添加到Git：
$ git add -f App.class

可以用git check-ignore命令检查：
$ git check-ignore -v App.class
.gitignore:3:*.class	App.class
Git会告诉我们，.gitignore的第3行规则忽略了该文件，于是我们就可以知道应该修订哪个规则。

在.gitignore文件添加例外规则：
# 排除所有.开头的隐藏文件:
.*
# 排除所有.class文件:
*.class

# 不排除.gitignore和App.class:
!.gitignore
!App.class

把指定文件排除在.gitignore规则外的写法就是!+文件名，所以，只需把例外文件添加进去即可。

【小结】
忽略某些文件时，需要编写.gitignore；
.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理！

2、配置别名
（1）配置别名
用别名st表示status
$ git config --global alias.st status
用co表示checkout，ci表示commit，br表示branch：
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch

--global参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用。

命令git reset HEAD file可以把暂存区的修改撤销掉（unstage），重新放回工作区。
既然是一个unstage操作，就可以配置一个unstage别名：
$ git config --global alias.unstage 'reset HEAD'
当你敲入命令：
$ git unstage test.py
实际上Git执行的是：
$ git reset HEAD test.py

配置一个git last，让其显示最后一次提交信息：
$ git config --global alias.last 'log -1'

把lg配置成了：
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

（2）配置文件
配置Git的时候，加上--global是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。
配置文件放哪了？每个仓库的Git配置文件都放在.git/config文件中：
$ cat .git/config 
别名就在[alias]后面，要删除别名，直接把对应的行删掉即可。

3、搭建Git服务器
（1）搭建Git服务器
搭建Git服务器需要准备一台运行Linux的机器，强烈推荐用Ubuntu或Debian，这样，通过几条简单的apt命令就可以完成安装。
假设你已经有sudo权限的用户账号，下面，正式开始安装。
第一步，安装git：
$ sudo apt-get install git
第二步，创建一个git用户，用来运行git服务：
$ sudo adduser git
第三步，创建证书登录：
收集所有需要登录的用户的公钥，就是他们自己的id_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个。
第四步，初始化Git仓库：
先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令：
$ sudo git init --bare sample.git
Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，
所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。然后，把owner改为git：
$ sudo chown -R git:git sample.git
第五步，禁用shell登录：
出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行：
git:x:1001:1001:,,,:/home/git:/bin/bash
改为：
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
这样，git用户可以正常通过ssh使用git，但无法登录shell，因为我们为git用户指定的git-shell每次一登录就自动退出。
第六步，克隆远程仓库：
现在，可以通过git clone命令克隆远程仓库了，在各自的电脑上运行：
$ git clone git@server:/srv/sample.git
Cloning into 'sample'...
warning: You appear to have cloned an empty repository.
剩下的推送就简单了。
（2）管理公钥
如果团队很小，把每个人的公钥收集起来放到服务器的/home/git/.ssh/authorized_keys文件里就是可行的。
如果团队有几百号人，就没法这么玩了，这时，可以用Gitosis来管理公钥。
这里我们不介绍怎么玩Gitosis了，几百号人的团队基本都在500强了，相信找个高水平的Linux管理员问题不大。

（3）管理权限
有很多不但视源代码如生命，而且视员工为窃贼的公司，会在版本控制系统里设置一套完善的权限控制，
每个人是否有读写权限会精确到每个分支甚至每个目录下。因为Git是为Linux源代码托管而开发的，
所以Git也继承了开源社区的精神，不支持权限控制。不过，因为Git支持钩子（hook），所以，
可以在服务器端编写一系列脚本来控制提交等操作，达到权限控制的目的。Gitolite就是这个工具。
这里我们也不介绍Gitolite了，不要把有限的生命浪费到权限斗争中。

【小结】
搭建Git服务器非常简单，通常10分钟即可完成；
要方便管理公钥，用Gitosis；
要像SVN那样变态地控制权限，用Gitolite。














