# 创建Git仓库，以及添加文件到仓库 #

> 先来理解一个概念，repository。你可以简单将它理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。这个目录通常称为仓库，或者叫版本库。


## 本地新建一个git仓库 ##

通过 git init 命令把这个目录变成 Git 可以管理的仓库：

`git init`或者`git --bare init`

>如果是需要作为服务器仓库的，推荐加—bare，否则以后其他仓库push代码到此仓库时会有一些麻烦。

瞬间Git就把仓库建好了，而且告诉你是一个空的仓库（empty Git repository），细心的读者可以发现当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。

也不一定必须在空目录下创建Git仓库，选择一个已经有东西的目录也是可以的。不过，不建议你使用自己正在开发的公司项目来学习Git，否则造成的一切后果概不负责。

初始化后，在当前目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。不过目前，仅仅是按照既有的结构框架初始化好了里边所有的文件和目录，但我们还没有开始跟踪管理项目中的任何一个文件。

仓库的文件结构

我们看看这个目录下的文件结构：

├─hooks
│      applypatch-msg.sample
│      commit-msg.sample
│      post-update.sample
│      pre-applypatch.sample
│      pre-commit.sample
│      pre-push.sample
│      pre-rebase.sample
│      prepare-commit-msg.sample
│      update.sample
│      
├─info
│      exclude
│      
├─objects
│  ├─info
│  └─pack
└─refs
    ├─heads
    └─tags

该目录下有可能还有其他文件，但这是一个全新的 git init 生成的库，所以默认情况下这些就是你能看到的结构。config 文件包含了项目特有的配置选项，info 目录保存了一份不希望在 .gitignore 文件中管理的忽略模式 (ignored patterns) 的全局可执行文件。hooks 目录保存了客户端或服务端钩子脚本。

另外还有四个重要的文件或目录：HEAD 及 index 文件，objects 及 refs 目录。这些是 Git 的核心部分。

- objects 目录存储所有数据内容
- refs 目录存储指向数据 (分支) 的提交对象的指针
- HEAD 文件指向当前分支
- index 文件保存了暂存区域信息

## 添加文件到仓库 ##

> 首先这里再明确一下，所有的版本控制系统，其实只能跟踪文本文件的改动，比如TXT文件，网页，所有的程序代码等等，Git也不例外。版本控制系统可以告诉你每次的改动，比如在第5行加了一个单词“Linux”，在第8行删了一个单词“Windows”。而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，也就是只知道图片从100KB改成了120KB，但到底改了啥，版本控制系统不知道，也没法知道。

现在我们编写一个readme.txt文件，内容如下：


    Git is a version control system.
    Git is free software.

一定要放到刚才`git init`后的目录下（子目录也行），因为这是一个Git仓库，放到其他地方Git再厉害也找不到这个文件。

和把大象放到冰箱需要3步相比，把一个文件放到Git仓库只需要两步。

第一步，用命令git add告诉Git，把文件添加到仓库：

`$ git add readme.txt`

执行上面的命令，没有任何显示，这就对了，Unix的哲学是“没有消息就是好消息”，说明添加成功。

第二步，用命令git commit告诉Git，把文件提交到仓库：

    $ git commit -m "commit readme.txt"
    [master (root-commit) cb926e7] wrote a readme file
     1 file changed, 2 insertions(+)
     create mode 100644 readme.txt

简单解释一下git commit命令，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

嫌麻烦不想输入-m "xxx"行不行？确实有办法可以这么干，但是强烈不建议你这么干，因为输入说明对自己对别人阅读都很重要。

git commit命令执行成功后会告诉你，1个文件被改动（我们新添加的readme.txt文件），插入了两行内容（readme.txt有两行内容）。

为什么Git添加文件需要add，commit一共两步呢？因为commit可以一次提交很多文件，所以你可以多次add不同的文件，比如：
    
    $ git add file1.txt
    $ git add file2.txt
    $ git add file3.txt
    $ git commit -m "add 3 files."

小结

现在总结一下今天学的两点内容：

1. 初始化一个Git仓库，使用git init命令。并且了解到一个仓库的文件组成结构。如果是需要作为服务器仓库的，推荐加—bare。

2. 添加文件到Git仓库，分两步：

第一步，使用命令git add ，注意，可反复多次使用，添加多个文件；也可以git add file1 file2 file3 file4等。
第二步，使用命令git commit，完成。


当然我们也可以从远程已有的仓库进行`clong`，这样我们也可以得到一个本地的仓库，不过，这个会在后边了解了远程仓库后再讲，今天就学习到三个命令，简单吧，这可是你以后会经常用到的哦，所以要熟记它们的用法。

