## 基本操作

### 推荐使用 [fork](https://git-fork.com/) GUI，相当好用，一对夫妻开发。啥时候我也能找一个程序员老婆?! 

* `git diff` 默认比较的是工作区(未暂存)和暂存区的差异。 `git diff --cached` 和 `git diff --staged` 命令比较的是暂存内容与提交内容的差异。

* 类似的 `git rm` 从暂存区移除被跟踪的文件，而 `git rm --cached` 则是从提交区移除文件。两者只是移除版本控制，并未删除文件。注意： `git rm --cached` 将文件直接移除版本控制，而不是回退到暂存区。

* `git commit -a -m` 可以将已经跟踪过的并再次发生变更的文档直接跳过 `git add` 命令进行提交。需要注意，在这种情况下提交的文件在之前已经通过 `git add` 被跟踪过。

* `git mv` 修改文件名，与 `mv` 命令不同的是，前者能够保留文件的版本管理数据，而后者相当于删除，重新创建了一个文件，新文件的版本管理信息并不继承于原文件。

* ### `git log` 
    - `git log --stat` 查看每次提交的统计性信息。
    - `git log -p` 查看每次提交内容的差异。
    - `git log --graph` 显示 ASCII 图形表示的分支合并历史。
    - `git log --pretty` 设置展示提交记录的格式：
      + `--pretty=oneline` 提交记录显示在一行。e.g: `6f417651b15b7e676b2d412f38341b9dee606f3c 首次提交` 
      + `--pretty=short` 增加显示了作者、邮件等信息。
      + `--pretty=full` 增加显示了提交者的信息。
      + `--pretty=fuller` 增加显示了相应的时间信息。
      + `--pretty=format` 自定义 log 的显示信息，[详情](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)。

* 撤销操作

    - 撤销暂存文件发生的修改： `git reset HEAD fileName` 
    - 撤销对文件的暂存( `git add` )： `git checkout -- filename` 
    - `git commit --amend` ：如果暂存区未发生变化，则修改上次的提交信息，如果暂存区发生变化，则将该变化与上次提交合并成一次提交。

* 远程仓库管理

  + `git remote -v` ，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。

  + `git remote add <shortname> <url>` 添加一个新的远程 Git 仓库，*url* 指示远程仓库的位置，而 *shortname* 则表示该远程仓库的简写名。 `git remote rename oldName newName` 命令更改远程仓库的简写。 `git remote remoteName` 命令移除远程仓库。

  + `clone` 命令克隆了一个仓库，命令会自动将克隆的仓库添加为远程仓库并默认以 “origin” 为简写。

  + 如果想要查看某一个远程仓库的更多信息，可以使用 `git remote show [remote-name]` 命令。可以显示本地仓库与远程仓库是否同步等信息。*这个命令列出了当你在特定的分支上执行 git push 会自动地推送到哪一个远程分支。 它也同样地列出了哪些远程分支不在你的本地，哪些远程分支已经从服务器上移除了，还有当你执行 git pull 时哪些分支会自动合并。*

* `git config --global alias.co checkout` 为命令添加别名。

## 分支

*由于 Git 的分支实质上仅是包含所指对象校验和（长度为 40 的 SHA-1 值字符串）的文件，所以它的创建和销毁都异常高效。 创建一个新分支就相当于往一个文件中写入 41 个字节（40 个字符和 1 个换行符），如此的简单能不快吗？*

* `git branch [branch-name]` 创建分支

* `git branch -d [branch-name]` 删除分支

* `git checkout [branch-name]` 切换到分支指定，**分支切换会改变你工作目录中的文件**，***在切换分支时，一定要注意你工作目录里的文件会被改变。 如果是切换到一个较旧的分支，你的工作目录会恢复到该分支最后一次提交时的样子。 如果 Git 不能干净利落地完成这个任务，它将禁止切换分支。***

* `git checkout -b [branch-name]` 创建并切换到指定分支

* `git log --oneline --decorate` 该命令用于查看各个分支当前所指的对象，HEAD 可以看作当前所指对象的别名

* `git log --oneline --decorate --graph --all`*它会输出你的提交历史、各个分支的指向以及项目的分支分叉情况*

### [分支合并](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6)

* 让我们来看一个简单的分支新建与分支合并的例子，实际工作中你可能会用到类似的工作流。 你将经历如下步骤：

  + 开发某个网站。

  + 为实现某个新的需求，创建一个分支。

  + 在这个分支上开展工作。

  正在此时，你突然接到一个电话说有个很严重的问题需要紧急修补。 你将按照如下方式来处理：

  + 切换到你的线上分支（production branch）。

  + 为这个紧急任务新建一个分支，并在其中修复它。

  + 在测试通过之后，切换回线上分支，然后合并这个修补分支，最后将改动推送到线上分支。

  + 切换回你最初工作的分支上，继续工作。

  + `git merge [branch-name]` 该指令的实质就是将指向当前分支的指针指向 [branch-name] 指针指向的节点。 `--merged` 与 `--no-merged` 这两个有用的选项可以过滤这个列表中已经合并或尚未合并到当前分支的分支，当然实在没有删除分支的情况下有效。

  + *在合并的时候，你应该注意到了"快进（fast-forward）"这个词。 由于当前 master 分支所指向的提交是你当前提交（有关 分支的提交）的直接上游，所以 Git 只是简单的将指针向前移动。 换句话说，当你试图合并两个分支时，如果顺着一个分支走下去能够到达另一个分支(一个分支指向的提交是另一个分支指向提交的父亲节点)，那么 Git 在合并两者的时候，只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”。*

  + 与 “fast-forward” 不一样的是：当两个分支共享一个共同的父节点时, *和之前将分支指针向前推进所不同的是，Git 将此次三方合并的结果做了一个新的快照并且自动创建一个新的提交(默认提交信息为 “Merge branch [branch-name]”)指向它(该新的提交有两个指针，分别指向两个分支)。 这个被称作一次合并提交，它的特别之处在于他有不止一个父提交。*但是当两个分支对同一个文件的操作产生差异时会产生冲突，这时候就需要手动解决冲突。  解决冲突之后需要使用命令 `git commit` 发布合并，这时候就可以产生合并记录了。当没有冲突发生时，两个分支的内容均会出现在工作目录中。

  

### [远程分支](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AF)

* `git push origin [new-branch]` 当远程仓库不存在分支 `new-branch` ,则该命令会在远程创建一个分支 `new-branch`。

* `git push origin --delete [remote-branch]` 删除远程仓库的 `remote-branch` 分支，`git branch -d [local-branch]` 该命令用于删除本地分支。

* *如果并不想让远程仓库上的分支叫做 serverfix，可以运行 `git push origin serverfix:awesomebranch` 来将本地的 serverfix 分支推送到远程仓库上的 awesomebranch 分支。下一次其他协作者从服务器上抓取数据时，他们会在本地生成一个远程分支 origin/serverfix，指向服务器的 serverfix 分支的引用*。**要特别注意的一点是当抓取到新的远程跟踪分支时，本地不会自动生成一份可编辑的副本（拷贝）(也就是说本地无法对远程分支进行编辑，只能将远程分支作为父节点)。 换一句话说，这种情况下，不会有一个新的 serverfix 分支——只有一个不可以修改的 origin/serverfix 指针。** 可以新建一个本地分支并运行 `git merge origin/serverfix` 将这些工作合并到当前所在的分支（这时候就可以在本地对文件进行编辑）。 如果想要在自己的 serverfix 分支上工作，可以将其建立在远程跟踪分支之上, 这会给你一个用于工作的本地分支，并且起点位于 origin/serverfix。

``` 
  $ git checkout -b serverfix origin/serverfix
  Branch serverfix set up to track remote branch serverfix from origin.
  Switched to a new branch 'serverfix'
  
```

* `git branch -vv` 命令列出本地分支的信息，包括分支跟踪的是远程分支的信息

* *当克隆一个仓库时，它通常会自动地创建一个跟踪 origin/master 的 master 分支。 然而，如果你愿意的话可以设置其他的跟踪分支，或是一个在其他远程仓库上的跟踪分支，又或者不跟踪 master 分支。 最简单的实例就是像之前看到的那样，运行 git checkout -b [branch] [remotename]/[branch]。 这是一个十分常用的操作所以 Git 提供了 --track 快捷方式：*

``` shell
git checkout --track origin/serverfix
```

* 如果想要将本地分支与远程分支设置为不同名字，你可以轻松地使用上一个命令增加一个不同名字的本地分支： `git checkout -b sf origin/serverfix` 现在，本地分支 sf 会自动从 origin/serverfix 拉取。

* 你可以在任意时间使用 -u 或 --set-upstream-to 选项运行 git branch 来显式地设置。 `git branch -u origin/serverfix` 。

### [分支-变基](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)

*在 Git 中整合来自不同分支的修改主要有两种方法：`merge` 以及 `rebase`。整合分支最容易的方法是 merge 命令。 它会把两个分支的最新快照以及二者最近的共同祖先进行三方合并，合并的结果是生成一个新的快照（并提交）。rebase 命令将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样*。***这两种整合方法的最终结果没有任何区别，但是变基（rebase）使得提交历史更加整洁。你在查看一个经过变基的分支的历史记录时会发现，尽管实际的开发工作是并行的，但它们看上去就像是串行的一样，提交历史是一条直线没有分叉***。*一般我们这样做的目的是为了确保在向远程分支推送时能保持提交历史的整洁——例如向某个其他人维护的项目贡献代码时。 在这种情况下，你首先在自己的分支里进行开发，当开发完成时你需要先将你的代码变基到 origin/master 上，然后再向主项目提交修改。 这样的话，该项目的维护者就不再需要进行整合工作，只需要快进合并便可*。


### 常用命令整理

+ `git branch -f master HEAD~3`：*上面的命令会将 master 分支强制指向 HEAD 的第 3 级父提交*。该命令不能在 master 分支下运行，必须切换到其他分支之后再执行命令。
    
+ 虽然在你的本地分支中使用 `git reset` 很方便，但是这种“改写历史”的方法对大家一起使用的远程分支是无效的。为了撤销更改并分享给别人，我们需要使用 `git revert`,要撤销的提交记录后面居然多了一个新提交，该新提交则是保存了`revert` 这一操作。
