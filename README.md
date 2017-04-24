# Git入门学习
主要参考自：http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000

1.git设置：
	安装完git后，先要进行user.name和user.email的设置：
		git config --global user.name "username"
		git config --global user.email "email"
		注意：--global是全局设置，username和email保存在了用户主目录下的.gitconfig文件中，对于当前用户的所有本地仓库全有效，倘若某个本地仓库要使用其它的username和email，只需要在为这个仓库设置时不加--global参数就可以了。

2.创建git本地仓库：
	新修建一个空目录; git init--->初始化为git可以管理的本地仓库，执行完后会在当前目录下出现一个隐藏目录.git，即本地仓库。

3.创建文件readme.txt（一定是文本文件，不能是二进制文件）
	vi readme.txt--->添加文本，进行修改
	git add readme.txt--->添加到.git目录中的暂存区stage，可以被Git管理
	git commit -m "版本说明" --->提交到当前分支上去，HEAD指针指向当前分支的上最新提交版本
    Git的每次commit都会生成一个SHA-1 值（40位16进制表示），通常情况下不需要所有位来唯一标识每次commit，可以为你的SHA-1值生成出简短且唯一的缩写。如果你传递 --abbrev-commit 给git log 命令，输出结果里就会使用简短且唯一的值；它默认使用七个字符来表示，不过必要时为了避免 SHA-1 的歧义，会增加字符数：
	如：
	git log --pretty=oneline --abbrev-commit
	ca82a6d changed the version number
	085bb3b removed unnecessary test code
	a11bef0 first commit
	通常在一个项目中，使用八到十个字符来避免SHA-1歧义已经足够了。最大的 Git 项目之一，Linux 内核，目前也只需要最长40个字符中的12个字符来保持唯一性。

4.时光穿梭机：
     git diff readme.txt  /  git diff HEAD -- readme.txt--->查看版本库最新版本与当前修改后文件的差别
	 git log--->查看历史提交  --pretty=oneline--->一行显示
  	 git reflog--->查看历史命令
  	 git reset --hard commitID--->穿梭到过去或重返回未来的某个版本
  	 git reset --hard HEAD^--->回退到上一个版本，HEAD指向的版本代表当前版本，HEAD^代表上一个提交版本，HEAD^^代表上上一个提交版本，当然版本很多时就用HEAD～100表示之前的第100					

5.Git中暂存区与分支的概念。

6.Git管理的是修改，而不是文件，所以每次对文件的修改一定要先add到暂存区，然后才能commit到当前分支上，否则所做的修改就会丢失。

7.撤销修改：
      撤销对文件的修改：git checkout -- readme.txt--->撤销对readme.txt文件的修改。
	  撤销已add到暂存区的修改：git reset HEAD readme.txt--->把暂存区的修改回退到工作区。
	  撤销已commit到分支上的修改：版本回退，前提是本地仓库还未提交到远程仓库。

8.删除文件：删除工作区的文件，rm -rf readme.txt，本地仓库会知道(git status)有文件被修改了，确实要删除这个文件，则git rm readme.txt从本地仓库中也删除，最后git commit -m "remove readme.txt"清空工作区的修改；否则撤销删除（误删）git checkout -- readme.txt恢复到版本库的最新版本，注意只能恢复到最新版本，意味着最后一次提交后所做的修改就会丢失。

上述是本地仓库的一些操作。

////////////////////////////////////////////////////////////////////////////////////////

下面开始本地仓库和远程仓库（Github）的协作。

Git本地仓库与Github远程仓库通过ssh协议进行加密传输，需要进行设置。
第1步：创建SSH Key。ssh-keygen -t rsa -C "youremail@example.com"；在用户主目录下生成.ssh隐藏目录，里面有两个文件id_rsa和id_rsa.pub，这两个是SSH的密钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心的告诉别人。
第2步：登陆远程仓库GitHub，打开Account settings---->SSH Keys---->Add SSH Key:填写title,Key中粘贴公钥内容---->Add Key.
       GitHub通过SSH Key（只要公钥）识别推送只能由我自己进行，别人不能冒充，如果有多台电脑要向一个远程仓库推送，只要把每台电脑的公钥都添加到GitHub中即可。GitHub上免费托管的Git仓库，对所有人都是可见的（public选项），但只有自己才能修改。

将本地Git仓库添加到远程仓库：
为远程仓库GitHub设置好SSH Key后，就可以对本地Git仓库与远程仓库进行关联。
首先要创建一个远程仓库，Create Repository，但这只是一个空的仓库，先要与本地仓库关联，然后才能把本地仓库的内容推送上去。
git remote add origin git@github.com:xxxxxx/learn.git--->远程库名字默认是origin   xxxxxx:GitHub账户名
git push -u origin master--->把本地仓库的内容推送到远程仓库上去，实际上是把master分支推送上去。-u参数：我们第一次推送master分支时，不但会把本地master分支的内容推送到远程新的mater，还会把这两个master分支进行关联，从而实现跟踪。之后就不需要添加-u了，直接git push origin master就可以了。

从远程仓库克隆到本地仓库：
先创建一个远程仓库，建议勾选初始化READEME.md文档。
git clone git@github.com:chenqb9311/gitskills.git--->在本机上克隆出一个gitskills本地Git仓库。
注意：Git既支持Https协议，也支持SSH协议，不过Https协议传输较慢，且每次推送都要口令，不方便；相比之下，Git原生支持的SSH协议则更好，效率更高，所以优先使用SSH协议。

////////////////////////////////////////////////////////////////////////////////////////

接下来详细聊一下Git中很重要的分支：
分支在Git中非常重要，团队中多人协作的开发就离不开分支。比如我需要开发某项功能，但需要一定的时间，为了不影响其他人的继续开发，我自己可以创建一个属于我的分支，然后在我自己的分支上工作，提交也是提交到我自己的分支上，当开发完成后，再合并到原来的分支上并把我自己的分支删除就可以了。

创建与合并分支：
SVN中也有分支，但是很慢，而Git中的分支，无论是创建、合并还是删除，速度都非常快。
默认情况下只有一个master分支，HEAD实际指向的是master,而master指向的才是当前最新的提交点，创建新的分支也仅仅是创建一个新的指针指向master所指向的当前最新提交点而已。
创建分支：git branch dev--->创建dev指针，指向master指向的当前最新提交点
切换到指定分支：git checkout dev--->HEAD切换到指向dev指针
创建并切换到指定分支：git checkout -b dev
查看本地仓库中的所有分支：git branch ，其中带*的就带表当前所在的分支
注意：在dev分支上做的修改和提交（dev指针移动，master指针不会动）对master分支是不可见的（因为master分支当前的最新提交点还没变），除非进行合并。
将dev分支合并到master分支：git merge dev--->Fas-Forward代表合并是快进模式，直接将master指向dev最新的提交点即实现了合并，所以速度很快。当然，并不是每次合并都是快进模式。
删除分支：git checkout -d dev

解决分支冲突：
当在新创建的分支feature1上对工作区进行了修改提交，切换回master分支后又对工作区进行了修改提交，这时两个分支上各自都有新的提交，如果此时将feature1分支合并到master分支上，就无法执行快速合并，而且往往还会产生冲突，必须手动修复冲突后才能再提交。
git satus--->此时显示发生冲突的文件：both modified
cat readme.txt--->直接查看文件内容，Git会用<<<<<<<，=======，>>>>>>>标记出不同分支新的提交的内容，要将这些冲突修改保存后再提交即可，就不需要合并分支了，最后删除feature1分支即可。
git log --graph --pretty=oneline --abbrev-commit--->可以查看分支的合并提交过程，分支合并图。
记住当Git无法自动合并分支时，就必须首先解决冲突。冲突解决后，再提交，合并完成。

分支管理策略：
通常情况下，merge会采用快进模式，但这种模式下，删除分支后，会丢掉分支信息。如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
git merge --no-ff -m "merge with no-ff" dev---->--no-ff:合并时禁用Fast forward模式。这样合并会创建一个新的commit，所以加上-m参数，添加描述信息。
此时：git log --pretty=oneline --abbrev-commit就可以查看到被合并分支的提交信息。
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。

Bug分支：
在开发的过程中，不可避免的会出现bug，那么就要及时的修复bug，在git中，就可以利用一个新的临时分支来修复bug，修复后合并分支，再把这个临时分支删除即可。
但是在创建新的bug修复分支前，当前dev分支上的工作只进行到一半，还不能提交，但bug要求立即进行修复，怎么办？
git stash--->保存当前dev分支上的工作现场，等修复完bug后再恢复现场继续工作。
此时，git status--->工作区就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。
要确定在哪个分支上修复bug，假定要在master分支上修复，就在master分支上创建临时分支：
git checkout master--->git checkout -b issue-101
现在就修复bug，修改工作区文件，然后提交。add... commit...
修复完成，切换到naster分支，完成合并，最后喊出issue-101分支即可。
git checkout master--->git merge --no-ff -m "merge bug fix 101" issue-101--->git branch -d issue-101.
最后，切换回dev分支继续之前还未完成的工作。
git checkout dev
git status--->发现工作区还是干净的，刚才保存的工作现场存到哪了？
git stash list--->查看到工作现场还是存在某个地方的，需要进行恢复，有两个办法：
1：git stash apply:恢复后stash内容不会删除，需要git stash drop来删除。git stash apply stash@{0}--->恢复到指定的stash。
2：git stash pop--->恢复的同时把stash内容也删除了。
再用git stash list查看就看不到任何stash内容了。


添加新的功能：
在当前开发工作中，要添加一项新的功能，通常情况下不能破坏主分支，所以每添加一个新功能，最好新建一个feature分支，在feature分支上进行开发，开发完成后，合并，最后删除该分支即可。
创建切换分支均与之前命令一致。
这里学习一个新的命令：
倘若在合并前，由于某种原因，新的功能不能添加了，那么就要直接删除feature分支：
git branch -d feature--->销毁失败。Git友情提醒，feature分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用命令git branch -D feature--->强行删除feature分支（通常用来丢弃一个没有被合并过的分支）。

多人协作开发：
当你从远程仓库克隆时，实际上Git自动把克隆得到的本地仓库的master分支和远程仓库的master分支关联起来了，并且，远程仓库的默认名称是origin。
git remote--->查看远程仓库的信息
git remote -v--->显示更详细的远程仓库信息，包括可以抓取（fetch）和推送（push）的远程仓库origin地址，如果没有推送权限，就看不到push的地址。
推送分支：
推送分支，实际上就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：
推送master分支：git push origin master  推送dev分支：git push origin dev
但是，并不是一定要把本地分支都往远程推送，那么，哪些分支需要推送，哪些不需要呢？
1.master分支是主分支，因此要时刻与远程同步；
2.dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
3.bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
4.feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，根据需要而定。
抓取分支：
多人协作时，大家都会往master和dev分支上推送各自的修改。

现在，模拟一个你的小伙伴，可以在另一台电脑（注意要把SSH Key添加到GitHub，这样他才能推送）或者同一台电脑的另一个目录下（模拟另一个人克隆得到的本地仓库）克隆：
git clone git@github.com:chenqb9311/learngit.git--->和之前的克隆一样。
当你的小伙伴从远程库clone时，默认情况下，他只能看到本地的master分支：
git branch
* master
但他需要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：
git checkout -b dev origin/dev--->多了一个origin/dev,要注意
现在，他就可以在dev上继续修改，然后，时不时地把dev分支push到远程：
git add...--->git commit -m...--->git push origin dev
现在，小伙伴已经向origin/dev分支推送了他的提交，而碰巧我也对同样的文件作了修改，并试图推送，
但是会推送失败，因为小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用git pull把最新的提交从origin/dev抓取下来，然后，在我的本地仓库合并，解决冲突，再推送：
第一次git pull会失败，原因是还没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：git branch --set-upstream dev origin/dev，然后再pull即可。这回的git pull成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的解决冲突完全一样。解决后，提交，再push到远程dev分支即可。
总结一下：
因此，多人协作的工作模式通常是这样：
首先，可以试图用git push origin branch-name推送自己的修改；
如果推送失败，则因为远程分支比你的本地更新（比如其它人已经推送了最新提交到同一个远程分支上），需要先用git pull试图将远程分支上的更新与本地最新提交进行合并；
如果合并有冲突，则解决冲突，并在本地提交；
没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！
如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。
这就是多人协作的工作模式，一旦熟悉了，就非常简单。
参考：http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013760174128707b935b0be6fc4fc6ace66c4f15618f8d000

标签管理：
在发布每个版本时，都应为这个版本（就是一次commit）添加一个标签。
创建标签：
切换需要打标签的分支上：git checkout master--->git tag v1.0（标签v1.0默认是打在当前分支的最新提交版本commit上的，即HEAD指向版本）
git tag--->查看当前分支上的所有标签
如果要为历史commit版本打标签：git log --pretty=oneline abbrev-commit---->找到历史版本的commit ID--->
git tag v0.9 ab54y16--->在commit ID为ab54y16的commit上打上v0.9标签。
注意：git tag查看所有标签时，标签不是按时间顺序列出，而是按字母排序的。
git tag
v0.9（后打的）
v1.0（先打的）

git show v1.0--->查看标签1.0的详细信息。

git tag -a <tagname> -m "blablabla..."--->创建带有说明的标签，指定标签信息，-a指定标签名，-m指定标签说明
git tag -s <tagname> -m "blablabla..."可以用PGP签名标签，-s用私钥签名一个标签，签名采用PGP签名。

操作标签：
如果标签打错了，也可以删除：
git tag -d v0.1--->删除当前分支上的v0.1标签
创建的标签都只存储在本地仓库，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

git push origin v1.0--->推送v1.0标签到远程origin仓库。
git push origin --tags--->一次性推送全部尚未推送到远程的本地标签。

若标签以推送到远程，如何删除？
git tag -d v1.0--->先在本地删除
git push origin :refs/tags/v1.0--->然后从远程删除
最后可以登录远程github仓库进行确认是否真的删除了。


使用GitHub:
可以发布自己的开源项目，让别人参与；也可以参与别人的开源项目。
之前的都是发布自己的开源项目的内容，那么如何参与别人的开源项目呢？
1.找到要参与的开源项目，Fork--->从别人的远程仓库克隆到自己的远程仓库；（例如bootstrap项目）
2.从自己的远程仓库地址中把Fork到的开源项目克隆到本地仓库，一定要从自己的远程仓库中clone，这样你才能推送修改。如果从bootstrap的作者的仓库地址git@github.com:twbs/bootstrap.git克隆，因为没有权限（公钥），你将不能推送修改。
3.在本地克隆到的仓库中添加新的功能，只能推送到自己的远程仓库中，如果希望bootstrap官方的远程仓库接受，需要发起一个pull request，看人家是否接受我的修改。
总结：
在GitHub上，可以任意Fork开源仓库；
自己拥有Fork后的仓库的读写权限；
可以推送pull request给官方仓库来贡献代码。

配置Git:
新创建一个本地仓库后（包括新建一个目录，或者在其他电脑上从已有的远程仓库中克隆下来一个本地仓库都要设置user.name和user.email，其实就相当于另一个人的本地仓库，在与我共用的同一个远程仓库中添加他的SSH Key，那么他也可以推送。
git config --global color.ui true--->让Git适当地显示不同的颜色


忽略特殊文件：
有些时候，你必须把某些文件放到Git工作目录中，但又不想Git对它们进行管理，不能提交它们到远程，比如保存了数据库密码的配置文件啦，等等，每次git status都会显示Untracked files ...。
好在Git考虑到了大家的感受，这个问题解决起来也很简单，在Git工作区的根目录（版本库）下创建一个特的.gitignore文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

不需要从头写.gitignore文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：https://github.com/github/gitignore

忽略文件的原则是：
忽略操作系统自动生成的文件，比如缩略图等；
忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。
小例子参考：http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013758404317281e54b6f5375640abbb11e67be4cd49e0000
写好.gitignore后，还要把.gitignore也提交到Git进行管理。当然我们需要检验.gitignore是否书写正确，是否真的忽略了相应的文件，检验标准就是git status命令是不是说working directory clean。

有些时候，你想添加一个文件到Git本地仓库进行管理（.git目录），但发现添加不了，原因是这个文件被.gitignore忽略了：
git add App.class--->The following paths are ignored by one of your .gitignore files: App.class
Use -f if you really want to add them.
如果你确实想添加该文件，可以用-f强制添加到Git：git add -f App.class
或者你发现，可能是.gitignore写得有问题，需要找出来到底哪个规则写错了，可以用git check-ignore命令检查：
git check-ignore -v App.class--->.gitignore:3:*.class  App.class
Git会告诉我们，.gitignore的第3行规则忽略了该文件，于是我们就可以知道应该修订哪个规则。

为Git命令设置别名：
git config --global alias.st status--->st表示status
git config --global alias.co checkout--->co表示checkout
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.unstage 'reset HEAD'--->unstage(撤销)表示reset HEAD
git reset HEAD hello.py 等价于 git unstage hello.py 
--global参数是全局参数，也就是这些命令别名在这台电脑的所有Git仓库下都能使用。
加上--global是针对当前用户（当前电脑）的所有仓库起作用的，如果不加，那只针对当前的仓库起作用。
配置一个git last，让其显示最后一次提交信息：
git config --global alias.last 'log -1'
这样，用git last就能显示最近一次的提交。

每个仓库的Git配置文件都放在.git/config文件中，别名就在[alias]后面，要删除别名，直接把对应的行删掉即可。
而当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中，配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。

搭建Git服务器：以后有需要再学习。
解决GitHub Desktop无法下载的问题：
