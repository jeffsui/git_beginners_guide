# Gitolite
**Gitolite** 是用来托管 Git 的中心服务器，包含细颗粒度的权限控制及其他强大的特性。

## 前言
1. 整篇内容基本上是参照[官网文档][1] 的一小部分来写的，适当添加一些图片、个人想法，欢迎批评指正。

2. 整篇内容是假设读者是个懂 Git 的本地基本操作但却在寻求 Linux 上搭建（具有严格权限管理的） Git 服务器的人。

3. 预备知识：
    + Git 的基本操作：本地提交、远程推送（包含远程仓库的添加、移除、修改等）。
    + 所用 Linux 发行版的包管理软件的使用，包括安装、卸载软件包及安装某软件包的依赖软件包等操作。
    + Linux 的 OpenSSH 安装、 sshd 守护进程的开启等。

3. **Gitolite** 最为强调的是授权，而不是认证。
    + 授权 (authorisation) 是询问你是想做什么然后确定你允不允许做。
    + 认证 (authentication) 是验证你的身份。

4. **Gitolite** 有两种模式 —— ssh 跟 http 来使用，但只能用一种。
    + ssh 是指用户通过服务器的 sshd 守护进程来认证用户自己的身份，http 则是用户与 web 服务器进行交流来认证。
    + 当认证成功后，sshd 或者 httpd 会把权限的控制权交给 Gitolite，然后它就会去弄清楚你能做什么。
    + 本篇暂时只介绍 ssh 模式。


## 准备
1. 本篇所用的为 VirtualBox 虚拟机系统 CentOS-7.1 x86_64。
    + `uname -a` 的输出如下
    ```
    Linux gitolite 3.10.0-327.3.1.el7.x86_64 #1 SMP Wed Dec 9 14:09:15 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
    ```
    + 此系统是个 Vagrant 的 VirtualBox 包，由 **[CHEF Release Engineering team][2]** 用自己的 [Bento 模板][3] 预构建的。
    + 使用此打包的系统的原因
        + 一是 Vagrant 的包比较方便使用。不必自己安装一个新的，而且一旦系统出错，可用打包好的系统完全替换出错的系统。
        + 二是自带了 OpenSSH 和 sshd 及 Virtualbox 的 nat 网上端口转发的配置。

2. 在安装之前，必须对即将安装 Gitolite 的系统安装及配置必要的软件，主要的是 Git 及 OpenSSH。本篇不涉及这些安装，如有不懂之处，请自行查阅本指南其他部分及搜索相关内容。

## 安装
1. 启动 CentOS，打开命令行或者是终端，进入 centos 的目录，`vagrant up` 启动虚拟机。
    + 注：用其他方式也是可行的，这里只是由于我的系统已经配置好了，所以用这个来启动。

2. 用终端模拟器或者是其他工具登录到虚拟机（或者直接在虚拟机里操作），并切换到 `root` 用户，我用的是 XShell。

    ![login][img-1]

3. 创建 git 用户，这个用户就是**托管用户**。并为 git 用户配置密码，这里为方便操作与演示，用 `git` 作密码。
    + 托管用户即当你完成安装后，其他的用户将用这样的 URL `git@server:reponame` 或者 `ssh://git@server/reponame`。
    + 请留意这些 url 的书写。网上的其他教程会用这样的 url `ss://git@server/~/repositories/reponame.git` 来书写，导致在推送远程库的时候会发生错误。
    + 这里请注意，我的例子里只是简单地添加了 git 用户，有需要的读者请自行查看 `adduser` 的帮助，以更好地配置。

    ![add-git-user][img-2]

4. 创建管理员的 ssh 密钥，用于管理 Gitolite。
    + 这里我就直接用 vagrant 这个用户来管理了，读者可用其他用户来管理，但不推荐用 root 来管理。
    + 生成密钥之后要让密钥加入 `ssh-agent` 里，这样在后面使用的时候简单许多。
    ```shell
    eval `ssh-agent -s`
    ssh-add ~/.ssh/id_vagrant
    ```
    + 生成密钥时我是没有设置密码短语的。如果设置了，那么在 `ssh-add` 这行命令执行时，会提示输入密码短语，过程是一样的。
    + 最后把公钥复制到 /tmp 目录，是为了后面操作方便。

    ![vagrant-key][img-3]

5. 切换到 git 用户，克隆 Gitolite 的源代码，安装并设置。
    + 切换时需要输入上面设置的 git 用户密码，因切换前用户是 vagrant。
    + 上面复制的公钥是用来配置管理 Gitolite 的。
    + `-pk` 即 `--pubkey`，是管理员的公钥。如果此公钥丢失或者出现其他意外问题，可用此参数重新配置公钥。
    + `setup` 这个选项有个参数是 `-a`，即`--admin`，用于指出管理员的名称。这里我没有用这个参数，默认会以添加的 `vagrant.pub` 中的 `vagrant` 为管理员名称。
    + 详细的 `setup` 请参考 `bin/gitolite setup -h`。

    ![setup-gitolite][img-4]

6. 查看所安装的 `Gitolite`
    + 切换回 `vagrant` 这个用户，由于密钥已经在前面添加了，`git ls-remote`的命令可直接读取“远程”仓库信息。

    ![show-remote][img-5]

## 简单管理
1. 添加仓库并做简单使用。
    + 添加库就是在 `conf/gitolite.conf` 里按格式加上库名即可，`RW+`是读取的完全权限的意思。
    + 关于权限的具体设置，可查看官网的详细文档。这里仅作简单的设置。

    ![clone-admin][img-6]

    ![new-repo][img-7]

    ![clone-new-repo][img-8]

2. 在 Windows 上克隆此仓库并做简单的操作。
    + 在 Windows 上用 `Git-For-Windows` 的 `git-bash` 生成一对叫 `id_windows` 的密钥。并用工具将其中的公钥传入虚拟机内，我用的是 XFtp，其他工具也是可以用的。

    ![generate-key][img-10]

    ![upload-key][img-14]

    + 在虚拟机里将上述密钥的公钥加入当前密钥管理目录 `keydir` 里，重命名为 `learngit.pub`，并修改 `learn_to_use` 仓库的权限。

    ![modify-repo][img-9]

    ![add-key][img-11]

    + 修改 Windows 的当前用户目录中 `.ssh` 文件夹下 config 文件的内容。如果此文件不存在，创建一个即可。

    ![ssh-config][img-12]

    其内容是为了让后面的操作简单化，主要是因为虚拟机做了端口转发。

    ![port][img-15]

    + 克隆仓库，写文件并提交，推送。

    ![clone-windows][img-13]

    + 在虚拟机里检验文件及权限。

    ![no-write][img-16]

## 小结
1. 在本篇中，**Gitolite** 的配置与使用并不麻烦，所要用到的东西都是比较基础的。但说实话，不太适合于没点基础的所谓“小白”。
2. 本篇只涉及简单的安装与管理，并不包含复杂的使用，如权限的详细控制、书写和如何与 Web 服务器共同使用等内容。


[1]: http://gitolite.com/gitolite/gitolite.html
[2]: https://github.com/chef
[3]: https://github.com/chef/bento
[4]: http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-7.1_chef-provisionerless.box
[img-1]: images/gitolite/gitolite-system-vagrant-root.png
[img-2]: images/gitolite/gitolite-add-git-user.png
[img-3]: images/gitolite/gitolite-vagrant-ssh-key-generate.png
[img-4]: images/gitolite/gitolite-setup-gitolite.png
[img-5]: images/gitolite/gitolite-show-remote.png
[img-6]: images/gitolite/gitolite-clone-admin.png
[img-7]: images/gitolite/gitolite-new-repo-conf.png
[img-8]: images/gitolite/gitolite-clone-new-repo.png
[img-9]: images/gitolite/gitolite-modify-repo-authorization.png
[img-10]: images/gitolite/gitolite-windows-key.png
[img-11]: images/gitolite/gitolite-add-windows-pubkey.png
[img-12]: images/gitolite/gitolite-ssh-config.png
[img-13]: images/gitolite/gitolite-clone-repo-on-windows.png
[img-14]: images/gitolite/gitolite-xftp-upload-pub.png
[img-15]: images/gitolite/gitolite-forward-port.png
[img-16]: images/gitolite/gitolite-check-no-write.png
