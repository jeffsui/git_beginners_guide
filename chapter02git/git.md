# 基本git安装

最早Git是在Linux上开发的，很长一段时间内，Git也只能在Linux和Unix系统上跑。不过，慢慢地有人把它移植到了Windows上。现在，Git可以在Linux、Unix、Mac和Windows这几大平台上正常运行了。

要使用Git，第一步当然是安装Git了。根据你当前使用的平台来阅读下面的文字：

## 在**Linux**上安装**Git**

1. 首先，你可以试着输入`git`，看看系统有没有安装Git：
    ```
    $ git
    The program 'git' is currently not installed. You can install it by typing:
    sudo apt-get install git
    ```

    像上面的命令，有很多Linux会友好地告诉你Git没有安装，还会告诉你如何安装Git。

    如果你碰巧用Debian或Ubuntu Linux，通过一条`sudo apt-get install git`就可以直接完成Git的安装，非常简单。

    > 老一点的Debian或Ubuntu Linux，要把命令改为`sudo apt-get install git-core`，因为以前有个软件也叫GIT（GNU Interactive Tools），结果Git就只能叫git-core了。由于Git名气实在太大，后来就把GNU Interactive Tools改成`gnuit`，`git-core`正式改为`git`。

2. 如果是其他 Linux 发行版，一般包管理系统里都会带有这个包，可用不同的命令来安装。
    > 如 fedora，可以直接 `sudo dnf install git` 或者 `sudo yum install git`
    > 如 archlinux，可以用 'sudo pacman -S git'来安装。


3. 现在 Linux 发行版的包管理系统搭载的不一定是最新版的 Git。如果需要最新版的 Git，那可以自行编译。
    首先要安装足够的软件包，即所谓的依赖。
    ```
    Ubuntu: sudo apt-get build-dep git
    Fedora: sudo dnf builddep git 或者是 sudo yum builddep git
    ```

    然后到 [Git 源码包网站][4] 上去下载最新版压缩包，下来后解压到一个文件夹里。
    之后就是 `./configure`，`make all`（如果要文档，可以 `make all doc`），最后要 `sudo make install`。

## 在**Mac OS X**上安装**Git**

如果你正在使用Mac做开发，有两种安装Git的方法。

一是安装homebrew，然后通过homebrew安装Git，具体方法请参考homebrew的文档：http://brew.sh/。

第二种方法更简单，也是推荐的方法，就是直接从AppStore安装Xcode，Xcode集成了Git，不过默认没有安装，你需要运行Xcode，选择菜单“Xcode”->“Preferences”，在弹出窗口中找到“Downloads”，选择“Command Line Tools”，点“Install”就可以完成安装了。

![Install安装][1]

Xcode是Apple官方IDE，功能非常强大，是开发Mac和iOS App的必选装备，而且是免费的！

## 在**Windows**上安装**Git**

### 安装版 MsysGit
Windows 下要使用 Linux/Unix 的工具时，需要 Cygwin 或者 msys 这样的 unix 模拟环境，Git 也一样。Cygwin 的安装和配置都比较复杂，折腾的时间估计会比较长。不过，有高人已经把模拟环境和 Git 都打包好了，名叫 msysgit，只需要下载一个单独的exe安装程序，从 `http://msysgit.github.io/` 下载，然后按默认选项安装即可。

安装完成后，在开始菜单里找到“Git”->“Git Bash”，蹦出一个类似命令行窗口的东西，就说明Git安装成功！

![WINDOWS安装][2]

### 便携版 MsysGit

便携版是给相对熟悉 Windows 命令行和环境变量的人使用的。从上面的那个网址中下载来 PortableGit 后，用 7-zip 或者直接双击打开后，解压到一个**非中文且无空格的**的路径，然后把路径中的 cmd 文件夹加入环境变量 PATH 中。**注：请注意，不懂得环境变量或者只想用鼠标操作的新手不建议用这种方式来使用 Git**。

> 以上部分引用 [瘳雪峰的官方网站--安装Git][7]。

另注：
> Git 2.0的 Windows 移植版本也在积极的开发中，只不过现在还不算很稳定。有兴趣的人可以去关注一下 [git for windows][6]。

# Git 基本设置
安装完成后，还需要最后一步基本的设置，在命令行输入：
```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址。你也许会担心，如果有人故意冒充别人怎么办？这个不必担心，首先我们相信大家都是善良无知的群众，其次，真的有冒充的也是有办法可查的。

> 其中有一个办法是使用 gnupg 制作一个专用于 Git 提交的密钥，具体怎么做，这里不作介绍。
> 有兴趣的可以查看 [Pro Git][5]。
> 密钥制作好后，设置时就直接 `$ git config --global user.signingkey XXXXXXXX`，就设置了唯一的用户对应的密钥。
> 但git 1.x的版本并没有自动在提交时添加密钥的选项，提交时需要手动添加 -S 参数。
> 而在git 2.x加入一个选项， `$ git config --global commit.gpgsign true`，满足自动加密钥签名。

Git 的设置是比较复杂的，1.x 的版本跟 2.x 的版本有些差异。但总的来说，要设置的一般也就那么几个，用户名、邮件地址、跨平台换行符的问题、合并策略和一些别名（即简单的命令组合，比如把 `checkout` 缩写成 `co`）等。

1. `git config` 有多级分层的设置，层次从高到低是 `系统` -- `用户` -- `仓库` -- `文件` 。
    + `--system` 参数，是系统级别的设置，表示操作系统的所有用户的默认设置。
    + `--global` 参数，是用户级别的设置，表示操作系统上所有的属于你这个用户的 Git 仓库都会默认使用的配置，当然也可以对某个仓库指定不同的用户名和Email地址。
    + `--local` 参数或者无参数，是仓库级别的设置且在仓库目录里设置。
    + `-f` 或 `--file` 参数，是文件级别的设置，指定文件作为设置，覆盖环境变量 `GIT_CONFIG`。

2. 设置文件列表
    + `$(prefix)/etc/gitconfig` 是系统级别的设置文件
    + `$XDG_CONFIG_HOME/git/config` 这个是二级用户级别设置文件，一般不推荐创建与设置
    + `~/.gitconfig` 这个是用户级别的设置文件
    + `$GIT_DIR/config` 仓库级别的设置文件
    + 优先级 `仓库 > 用户 > 二级用户 > 系统`




  [1]: images/gitinstall/2015-6-11-1.jpg
  [2]: images/gitinstall/2015-6-11-2.jpg
  [3]: http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000
  [4]: https://www.kernel.org/pub/software/scm/git/
  [5]: https://git-scm.com/book/tr/v2/Git-Tools-Signing-Your-Work
  [6]: https://github.com/git-for-windows/git/releases
  [7]: http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000 "安装Git"
