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
