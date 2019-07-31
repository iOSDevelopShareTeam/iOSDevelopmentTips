# git常见命令及问题处理

## git 版本回退
* 在工作中会经常用到版本回退的问题，因为你在使用git提交当前版本的时候 你可能会commit到本地发现你commit出错了 这样你就需要回退到你那个正确的版本里面，这就用到了git版本回退命令。
* 要随时掌握工作区的状态，使用git status命令。
* 如果git status告诉你有文件被修改过，用git diff可以查看修改内容。
像这样，如果不断对文件进行修改，然后不断提交修改到版本库里，就好比玩RPG游戏时，每通过一关就会自动把游戏状态存盘，如果某一关没过去，你还可以选择读取前一关的状态。有些时候，在打Boss之前，你会手动存盘，以便万一打Boss失败了，可以从最近的地方重新开始。Git也是一样，每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为commit。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个commit恢复，然后继续工作，而不是把几个月的工作成果全部丢失。
当然了，在实际工作中，我们脑子里怎么可能记得一个几千行的文件每次都改了什么内容，不然要版本控制系统干什么。版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用git log命令查看：
需要友情提示的是，你看到的一大串类似3628164...882e1e0的是commit id（版本号），和SVN不一样，Git的commit id不是1，2，3……递增的数字，而是一个SHA1计算出来的一个非常大的数字，用十六进制表示，而且你看到的commit id和我的肯定不一样，以你自己的为准。为什么commit id需要用这么一大串数字表示呢？因为Git是分布式的版本控制系统，后面我们还要研究多人在同一个版本库里工作，如果大家都用1，2，3……作为版本号，那肯定就冲突了。
每提交一个新版本，实际上Git就会把它们自动串成一条时间线。如果使用可视化工具（比如source tree）查看Git历史，就可以更清楚地看到提交历史的时间线：
回退的时候注意：
首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交3628164...882e1e0（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
如果要回退到上一个版本的话 可以使用以下命令：
$ git reset --hard HEAD^
不过且慢，然我们用git log再看看现在版本库的状态：

最新的那个版本“xxxx”已经看不到了！好比你从21世纪坐时光穿梭机来到了19世纪，想再回去已经回不去了，肿么办？
办法其实还是有的，只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个“xxxx”的commit id是3628164...，于是就可以指定回到未来的某个版本：

$ git reset --hard 3628164
HEAD is now at 3628164 xxxx

果然，我胡汉山又回来了。
Git的版本回退速度非常快，因为Git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把HEAD从指向“append GPL”：
改为指向“add distributed”：
然后顺便把工作区的文件更新了。所以你让HEAD指向哪个版本号，你就把当前版本定位在哪。
现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的commit id怎么办？
在Git中，总是有后悔药可以吃的。当你用$ git reset --hard HEAD^回退到“add distributed”版本时，再想恢复到“append GPL”，就必须找到“append GPL”的commit id。Git提供了一个命令git reflog用来记录你的每一次命令：
$ git reflog
ea34578 HEAD@{0}: reset: moving to HEAD^
3628164 HEAD@{1}: commit: xxxx
ea34578 HEAD@{2}: commit: xxxx9999
cb926e7 HEAD@{3}: commit (initial): submit

终于舒了口气，第二行显示“xxxx”的commit id是3628164，现在，你又可以乘坐时光机回到未来了。
小结

现在总结一下：
* HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
* 穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
* 要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

## git 用法
### 问题1：

1. Git add 添加 多余文件 
这样的错误是由于， 有的时候 可能
git add . （空格+ 点） 表示当前目录所有文件，不小心就会提交其他文件
git add 如果添加了错误的文件的话
撤销操作
git status 先看一下add 中的文件 
git reset HEAD 如果后面什么都不跟的话 就是上一次add 里面的全部撤销了 
git reset HEAD XXX/XXX/XXX.m 就是对某个文件进行撤销了
2. git commit 错误
如果不小心 弄错了 git add后 ， 又 git commit 了。 
先使用 
git log 查看节点 
commit xxxxxxxxxxxxxxxxxxxxxxxxxx 
Merge: 
Author: 
Date:
然后 
git reset commit_id  就完成了
如果还没有 push 也就是 repo upload 的时候
git reset commit_id （回退到上一个 提交的节点 代码还是原来你修改的） 
git reset –hard commit_id （回退到上一个commit节点， 代码也发生了改变，变成上一次的）

3. 如果要是 提交了以后，可以使用 git revert
还原已经提交的修改 此次操作之前和之后的commit和history都会保留，并且把这次撤销作为一次最新的提交 
git revert HEAD 撤销前一次 commit 
git revert HEAD^ 撤销前前一次 commit 
git revert commit-id (撤销指定的版本，撤销也会作为一次提交进行保存） 
git revert是提交一个新的版本，将需要revert的版本的内容再反向修改回去，版本会递增，不影响之前提交的内容。
Git reset —hard  +提交号码
然后push
git push -f origin master
git checkout . #本地所有修改的。没有的提交的，都返回到原来的状态
git stash #把所有没有提交的修改暂存到stash里面。可用git stash pop回复。
git reset --hard HASH #返回到某个节点，不保留修改。
git reset --soft HASH #返回到某个节点。保留修改 
git clean -df #返回到某个节点
git clean 参数 -n 显示 将要 删除的 文件 和 目录 -f 删除 文件 -df 删除 文件 和 目录
git fetch  -p 更新分支列表

## 冲突解决

在使用git的时候出现的问题 是： merge  出现冲突
解决方案：找到冲突的位置
```
会有<<<<<<<header 
xxxxxxxxxxx //这里是自己的
＝＝＝＝＝＝＝＝
>>>>>>xxxxxxx
删掉 //这里是别人的
<<<<<<<header 
xxxxxxxxxxx 
＝＝＝＝＝＝＝＝
和
>>>>>>xxxxxxx  这样就可以了。
```
## git stash  用法

* git stash
保存当前的工作进度。会分别对暂存区和工作区的状态进行保存
* git stash save "message..."
这条命令实际上是第一条 git stash 命令的完整版
* git stash list
显示进度列表。此命令显然暗示了git stash 可以多次保存工作进度，并用在恢复时候进行选择
* git stash pop [--index] [<stash>]
如果不使用任何参数，会恢复最新保存的工作进度，并将恢复的工作进度从存储的工作进度列表中清除。
如果提供参数（来自 git stash list 显示的列表），则从该 <stash> 中恢复。恢复完毕也将从进度列表中删除 <stash>。
选项--index 除了恢复工作区的文件外，还尝试恢复暂存区。
* git stash apply [--index] [<stash>]
除了不删除恢复的进度之外，其余和 git stash pop 命令一样
* git stash clear
删除所有存储的进度





