# 基本 git 安装

最早 Git 是在 Linux 上开发的，很长一段时间内，Git 也只能在 Linux 和 Unix 系统上跑。不过，慢慢地有人把它移植到了 Windows 上。现在，Git 可以在 Linux、Unix、Mac 和 Windows 这几大平台上正常运行了。

要使用 Git，第一步当然是安装 Git 了。根据你当前使用的平台来阅读下面的文字：

## 在**Linux**上安装**Git**

1. 首先，你可以试着输入`git`，看看系统有没有安装 Git：
    ```
    $ git
    The program 'git' is currently not installed. You can install it by typing:
    sudo apt-get install git
    ```

    像上面的命令，有很多 Linux 会友好地告诉你 Git 没有安装，还会告诉你如何安装 Git。

    如果你碰巧用 Debian 或 Ubuntu Linux，通过一条`sudo apt-get install git`就可以直接完成 Git 的安装，非常简单。

    > 老一点的 Debian 或 Ubuntu Linux，要把命令改为`sudo apt-get install git-core`，因为以前有个软件也叫 GIT（GNU Interactive Tools），结果 Git 就只能叫 git-core 了。由于 Git 名气实在太大，后来就把 GNU Interactive Tools 改成`gnuit`，`git-core`正式改为`git`。

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

如果你正在使用 Mac 做开发，有两种安装 Git 的方法。

一是安装 homebrew，然后通过 homebrew 安装 Git，具体方法请参考 homebrew 的文档：http://brew.sh/。

第二种方法更简单，也是推荐的方法，就是直接从 AppStore 安装 Xcode，Xcode 集成了 Git，不过默认没有安装，你需要运行 Xcode，选择菜单“Xcode”->“Preferences”，在弹出窗口中找到“Downloads”，选择“Command Line Tools”，点“Install”就可以完成安装了。

![Install 安装][1]

Xcode 是 Apple 官方 IDE，功能非常强大，是开发 Mac 和 iOS App 的必选装备，而且是免费的！

## 在**Windows**上安装**Git**

Windows 下要使用 Linux/Unix 的工具时，需要 Cygwin 或者 msys 这样的 unix 模拟环境，Git 也一样。Cygwin 的安装和配置都比较复杂，折腾的时间估计会比较长。不过，有高人已经把模拟环境和 Git 都打包好了，名叫 [GitForWindows][6]，只需要下载一个单独的 exe 安装程序，从 `http://git-scm.com/download/win` 下载，然后按默认选项安装即可。

安装完成后，在开始菜单里找到“Git”->“Git Bash”，蹦出一个类似命令行窗口的东西，就说明 Git 安装成功！

![WINDOWS 安装][2]

> 以上部分引用 [瘳雪峰的官方网站 -- 安装 Git][7]。

# Git 基本设置
安装完成后，还需要最后一步基本的设置，在命令行输入：
```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
因为 Git 是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和 Email 地址。你也许会担心，如果有人故意冒充别人怎么办？这个不必担心，首先我们相信大家都是善良无知的群众，其次，真的有冒充的也是有办法可查的。

> 其中有一个办法是使用 gnupg 制作一个专用于 Git 提交的密钥，具体怎么做，这里不作介绍。
> 有兴趣的可以查看 [Pro Git][5]。
> 密钥制作好后，设置时就直接 `$ git config --global user.signingkey XXXXXXXX`，就设置了唯一的用户对应的密钥。
> 但 git 1.x 的版本并没有自动在提交时添加密钥的选项，提交时需要手动添加 -S 参数。
> 而在 git 2.x 加入一个选项， `$ git config --global commit.gpgsign true`，满足自动加密钥签名。

Git 的设置是比较复杂的，1.x 的版本跟 2.x 的版本有些差异。但总的来说，要设置的一般也就那么几个，用户名、邮件地址、跨平台换行符的问题、合并策略和一些别名（即简单的命令组合，比如把 `checkout` 缩写成 `co`）等。

1. `git config` 有多级分层的设置，层次从高到低是 `系统` -- `用户` -- `仓库` -- `文件` 。
    + `--system` 参数，是系统级别的设置，表示操作系统的所有用户的默认设置。
    + `--global` 参数，是用户级别的设置，表示操作系统上所有的属于你这个用户的 Git 仓库都会默认使用的配置，当然也可以对某个仓库指定不同的用户名和 Email 地址。
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
  [7]: http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000 "安装 Git"
