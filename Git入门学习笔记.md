@[TOC](Git入门学习笔记)
# 1. Git简介
Git是目前世界上最先进的分布式版本控制系统（没有之一）。
集中式版本控制系统，版本库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。`集中式版本控制系统最大的毛病是必须联网才能工作`。
分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。既然每个人电脑上都有一个完整的版本库，那多个人如何协作呢？比方说你在自己电脑上改了文件A，你的同事也在他的电脑上改了文件A，这时，你们俩之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。
和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库，某一个人的电脑坏掉了不要紧，随便从其他人那里复制一个就可以了。而集中式版本控制系统的中央服务器要是出了问题，所有人都没法干活了。
## 1. 安装Git
### 下载安装
我用的是win10，直接从Git官网安装的。[官方下载](https://git-scm.com/downloads)，然后默认安装即可。
### 设置
启动Git Bash
输入
```
git config --global user.name "your name"
git config --global user.email "email@example.com"
```
注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。
## 2. 创建版本库
版本库又名仓库，英文名repository，你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，`仅能跟踪文本文件`，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。
选择合适的地方，创建一个空目录：
```
# 创建空目录，目录中最好不要有中文和空格
mkdir learngit    # 创建文件夹
cd learngit    # 切换到该路径下
# 初始化，该目录授权给Git管理,会自动创建要给.git管理该目录
git init
# 注意编写文档最好用utf-8编码，如Notepad++，选择UTF-8 without BOM
```
## 3. 添加文件到仓库
只要两步，第一步用命令 `git add`告诉Git，把文件添加到仓库；
第二步，用命令`git commit`告诉Git，把文件提交到仓库。
```
Git add  <file_1>, <file_2>...    #一次可以添加多个文件
Git commit -m "<message>"
```
# 2. 时光机穿梭
## 1. 查看工作区状态与修改变化
`git status`：查看工作区状态。
`git diff`：查看修改的内容。
`cat <file>`：查看文件内容
## 2. 版本回退
`git log`：查看提交历史，便于决定回退版本，显示从最近到最远提交历史
`git log --prettty=oneline'：同`git log`，输出更简约
`git reset --hard commit_id`：在历史版本间穿梭。`HEAD`指向的版本就是当前的版本。
`git reflog`：查看命令历史，以便确定要回到未来的哪个版本。
## 3. 工作区（Working Directory）和暂存区（stage）
工作区：电脑里看到的目录。
版本库（Repository）：工作区中隐藏的`.git`，它是Git的版本库，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫`HEAD`。
我们把文件往Git版本库里添加的时候，是分两步执行的：
第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；
第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

## 4. 管理修改
Git跟踪并管理的是修改，而非文件。
`git diff HEAD -- readme.txt`：命令可以查看工作区和版本库里面最新版本的区别。
每次修改，如果不用`git add`到暂存区，那就不会加入到`commit`中。
## 5. 撤销修改
3个应用场景
场景1：未`git add <file>`，直接放弃工作区的修改。使用命令`git checkout -- <file>`
场景2：修改工作区，`git add <file>`到暂存区后，又修改了工作区。分两步回退，第一步`git reset HEAD <file>`，丢弃stage内容，回到场景1；第二步`git checkout -- <file>`，丢弃工作区修改。（顺序不能反）
场景3：已经`git commit`，只能用版本回退的方法，不过前提是没有推送到远程库。`git reset --hard commit_id`。
`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。
## 6. 删除文件
`rm <file>`：删除工作区文件，版本库中还存在
`git rm <file>`：删除版本库和工作区文件
`git commit` -m <message>：完成删除确认
`git checkout -- <file>`：撤销删除，前提是版本库中还有。
# 3. 远程仓库
## 1. 创建GitHub帐号并关联到本地电脑Git
第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：
`ssh-keygen -t rsa -C "youremail@example.com"`
把邮件地址换成自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。
如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：
然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容：
GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。
友情提示，在GitHub上免费托管的Git仓库，任何人都可以看到喔（但只有你自己才能改）。所以，不要把敏感信息放进去。
## 2. 添加远程库
1. 登录GitHub创建一个新的仓库；
2.使用命令`git remote add origin git@server-name:path/repo-name.git` 要关联一个远程库。
3. 关联后，使用命令`git push -u origin master`，第一次推送master分支的所有内容；
4. 此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改。
当你第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告。这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。
Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了。这个警告仅会出现一次。
分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！
## 3. 从远程库克隆
要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。
Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。
`git clone git@server-name:path/repo-name.git`
# 4. 分支管理
Git的分支是与众不同的，无论创建、切换和删除分支，Git在1秒钟之内就能完成！无论你的版本库是1个文件还是1万个文件。
## 1. 创建合并分支
Git鼓励大量使用分子，更加安全。
`git branch`：查看分支。
`git branch <name>`：创建分支
`git checkout <name>`：切换分支
`git checkout -b <name>`：创建并切换分支
`git merge <name>`：合并某分支到当前分支
`git branch -d <name>`：删除分支
## 2. 解决冲突
当Git无法自动合并分支时，发生冲突时，必须首先解决冲突。解决冲突后，再提交，合并完成。
解决冲突的方法就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。
`git log --graph`：可以看到分支合并图。
`git log --graph --pretty=oneline --abbrev-commit`：简略分支合并图
## 3. 分支管理策略
通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。
如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。`--no-ff`参数，表示禁用`Fast forward`：
`git merge --no-ff -m "merge with no-ff" dev`
分支策略
在实际开发中，我们应该按照几个基本原则进行分支管理：
首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；
你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。
所以，团队合作的分支看起来就像这样：

Git分支十分强大，在团队开发中应该充分应用。
## 4. Bug分支
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场。
`git stash list`：查看工作现场
`git  stash apply`：恢复，但是恢复后stash内容并不删除，有多个是可以用`git stash apply stash@{}`指定恢复stash。
`git stash drop`：删除stash内容
`git stah pop`：恢复现场，并删除stash内容
## 5. Feature 分支
开发一个新feature，最好新建一个分支；
如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。(实际中可以用`git stash `隐藏)
## 6. 多人协作
### 是否远程推送
并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？
1. master分支是主分支，因此要时刻与远程同步；
2. dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
3. bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
4. feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，视你的心情而定。
### 多人协作的工作模式
1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！
5. 如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。
`git remote -v`：查看远程库信息
`git checkout -b branch-name orgin/branch-name`：在本地创建和远程分支对应的分支，本地和远程分支的名称最好一致。
## 7. Rebase
rebase操作可以把本地未push的分叉提交历史整理成直线；
rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。
# 5. 标签管理
## 1. 创建标签
标签是为了便于我们找到特定的历史版本，方便管理。
`git tag <tagname>`：新建一个标签，默认为`HEAD`，也可以指定一个commit id，`git tag <tagname> <commit id>`;
`git tag -a <tagname> -m "blabla..."：`-a`指定标签名字，`-m`指定标签信息
`git tag`：查看所有标签
`git show <tagname>`：查看标签信息
注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。
## 2. 操作标签
`git push origin <tagname>`：推送一个本地标签到远程
`git push origin --tags`：推送本地全部未推送的标签到远程
`git tag -d <tagname>`：删除一个本地标签
`git push origin :refs/tags/<tagname>`：删除一个远程标签
# 6. 使用GitHub
在GitHub上，可以任意Fork开源仓库；
自己拥有Fork后的仓库的读写权限；
可以推送pull request给官方仓库来贡献代码。
# 7. 自定义Git
略
# 8. 学习资料
1. [廖雪峰老师的GIt教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)