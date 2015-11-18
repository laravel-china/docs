# Laravel Homestead

- [介绍](#introduction)
- [内置软件](#included-software)
- [安装与配置](#installation-and-setup)
- [常见用法](#daily-usage)
- [连接端口](#ports)
- [Blackfire Profiler](#blackfire-profiler)

<a name="introduction"></a>
## 介绍

Laravel 致力于让 PHP 开发体验更愉快，也包含你的本地开发环境。[Vagrant](http://vagrantup.com) 提供了一个简单、优雅的方式来管理与供应虚拟机。

Laravel Homestead 是一个官方预载的 Vagrant「封装包」，提供你一个美好的开发环境，你不需要在你的本机端安装 PHP、HHVM、网页服务器或任何服务器软件。不用担心搞乱你的系统！Vagrant 封装包可以搞定一切。如果有什么地方出现故障，你可以在几分钟内快速的销毁并重建虚拟机。

Homestead 可以在任何 Windows、Mac 或 Linux 上面运行，里面包含了 Nginx 网页服务器、PHP 5.6、MySQL、Postgres、Redis、Memcached 还有所有你要开发精彩的 Laravel 应用程序所需的软件。

> **附注：** 如果您是 Windows 的用户，您可能需要启用硬件虚拟化（VT-x）。通常需要通过 BIOS 来启用它。

Homestead 目前是构建且测试于 Vagrant 1.7 版本。

<a name="included-software"></a>
## 内置软件

- Ubuntu 14.04
- PHP 5.6
- HHVM
- Nginx
- MySQL
- Postgres
- Node (With Bower, Grunt, and Gulp)
- Redis
- Memcached
- Beanstalkd
- [Laravel Envoy](/docs/{{version}}/envoy)
- [Blackfire Profiler](#blackfire-profiler)

<a name="installation-and-setup"></a>
## 安装与配置

### 安装 VirtualBox / VMWare 与 Vagrant

在启动你的 Homestead 环境之前，你必须先安装 [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 和 [Vagrant](http://www.vagrantup.com/downloads.html). 两套软件在各平台都有提供易用的可视化安装程序。

#### VMware

除了 VirtualBox 之外, Homestead 也支持 VMware. 如果使用 VMware 作为 provider, 你需要购买 VMware Fusion / Desktop 以及 [VMware Vagrant plug-in](http://www.vagrantup.com/vmware). VMware 提供了更快、性能更好的共享文件夹。

### 增加 Vagrant 封装包

当 VirtualBox / VMware 和 Vagrant 安装完成后，你可以在终端机以下列命令将 'laravel/homestead' 封装包安装进你的 Vagrant 安装程序中。下载封装包会花你一点时间，时间长短将依据你的网络速度决定:

	vagrant box add laravel/homestead

如果这个命令失败了, 你可能安装的是一个老版本的 Vagrant 需要指定一个完整的 URL：

	vagrant box add laravel/homestead https://atlas.hashicorp.com/laravel/boxes/homestead

### 安装 Homestead

你可以简单地通过手动复制资源库的方式来安装 Homestead。将资源库复制至你的 "home" 目录中的 `Homestead` 文件夹，如此一来 Homestead 封装包将能提供主机服务给你所有的 Laravel（及 PHP）应用:

	git clone https://github.com/laravel/homestead.git Homestead

一旦你克隆完 Homestead 仓库，从 Homestead 目录中执行 `bash init.sh` 命令来创建 `Homestead.yaml` 配置文件:

	bash init.sh

此 `Homestead.yaml` 文件，将会被放置在你的 `~/.homestead` 目录中。

### 配置你的 Provider

在 `Homestead.yaml` 文件中的 `provider` 键表明需要使用的 Vagrant prodiver：`virtualbox` 、 `vmware_fusion` (Mac OS X)、或者 `vmware_workstation` (Windows)，你可以根据自己的喜好设定 provider 。

	provider: virtualbox

### 配置你的 SSH 密钥

然后你需要编辑 `Homestead.yaml`。可以在文件中配置你的 SSH 公开密钥，以及主要机器与 Homestead 虚拟机之间的共享目录。

如果没有 SSH 密钥的话， 在 Mac 和 Linux 下，你可以利用下面的命令来创建一个 SSH 密钥组:

	ssh-keygen -t rsa -C "you@homestead"

在 Windows 下，你需要安装 [Git](http://git-scm.com/) 并且使用包含在 Git 里的 `Git Bash` 来执行上述的命令。另外你也可以使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 和 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

一旦你创建了一个 SSH 密钥，记得在你的 `Homestead.yaml` 文件中的 `authorize` 属性指明密钥路径。

### 配置你的共享文件夹

`Homestead.yaml` 文件中的 `folders` 属性列出了所有你想在 Homestead 环境共享的文件夹列表。这些文件夹中的文件若有变动，他们将会同步在你的本机与 Homestead 环境里。你可以将你需要的共享文件夹都配置进去。

如果要开启 [NFS](http://docs.vagrantup.com/v2/synced-folders/nfs.html)，只需要在 `folders` 中加入一个标识：

	folders:
	    - map: ~/Code
	      to: /home/vagrant/Code
	      type: "nfs"

### 配置你的 Nginx 站点

对 Nginx 不熟悉？没关系。`sites` 属性允许你简单的对应一个 `域名` 到一个 homestead 环境中的目录。一个例子的站点被配置在 `Homestead.yaml` 文件中。同样的，你可以加任何你需要的站点到你的 Homestead 环境中。Homestead 可以为你每个进行中的 Laravel 应用提供方便的虚拟化环境。

你可以通过配置 `hhvm` 属性为 `true` 来让虚拟站点支持 [HHVM](http://hhvm.com):

	sites:
	    - map: homestead.app
	      to: /home/vagrant/Code/Laravel/public
	      hhvm: true

默认情况下, 每个站点在主机里面可以通过 HTTP 8000 和 HTTPS 44300进行访问

### Bash Aliases

如果要增加 Bash aliases 到你的 Homestead 封装包中，只要将内容添加到 `~/.homestead` 目录最上层的 `aliases` 文件中即可。

### 启动 Vagrant 封装包

当你根据你的喜好编辑完 `Homestead.yaml` 后，在终端机里进入你的 Homestead 文件夹并执行 `vagrant up` 命令。

Vagrant 会将虚拟机开机，并且自动配置你的共享目录和 Nginx 站点。如果要移除虚拟机，可以使用 `vagrant destroy --force` 命令。

为了你的 Nginx 站点，别忘记在你的机器的 `hosts` 文件将「域名」加进去。`hosts` 文件会将你的本地域名的站点请求指向你的 Homestead 环境中。在 Mac 和 Linux，该文件放在 `/etc/hosts`。在 Windows 环境中，它被放置在 `C:\Windows\System32\drivers\etc\hosts`。你要加进去的内容类似如下：

	192.168.10.10  homestead.app

务必确认 IP 地址与你的 `Homestead.yaml` 文件中的相同。一旦你将域名加进你的 `hosts` 文件中，你就可以通过网页浏览器访问到你的站点。

	http://homestead.app

继续读下去，你会学到如何连接到数据库！

<a name="daily-usage"></a>
## 常见用法

### 通过 SSH 连接

要通过 SSH 连接上您的 Homestead 环境，在终端机里进入你的 Homestead 目录并执行  `vagrant ssh` 命令。

因为你可能会经常需要通过 SSH 进入你的 Homestead 虚拟机，可以考虑在你的主要机器上创建一个"别名" 用来快速 SSH 进入 Homestead 虚拟机:

	alias vm="ssh vagrant@127.0.0.1 -p 2222"

一旦你创建了这个别名，无论你在主要机器的哪个目录，都可以简单地使用 "vm" 命令来通过 SSH 进入你的 Homestead 虚拟机。

你也可以在 Homestead 目录使用 `vagrant ssh` 命令。

### 连接数据库

在 `Homestead` 封装包中，已经预了 MySQL 与 Postgres 两种数据库。为了更简便，Laravel 的 `local` 数据库配置已经默认将其配置完成。

如果想要从本机上通过 Navicat 或者 Sequel Pro 连接 MySQL 或者 Postgres 数据库，你可以连接 `127.0.0.1` 的端口 33060 (MySQL) 或 54320 (Postgres)。而帐号密码分别是 `homestead` / `secret`。

> **附注：** 从本机端你应该只能使用这些非标准的连接端口来连接数据库。因为当 Laravel 运行在虚拟机时，在 Laravel 的数据库配置文件中依然是配置使用默认的 3306 及 5432 连接端口。

### 增加更多的站点

在 Homestead 环境上架且运行后，你可能会需要为 Laravel 应用程序增加更多的 Nginx 站点。你可以在单一个 Homestead 环境中运行非常多 Laravel 安装程序。有两种方式可以达成：第一种，在 `Homestead.yaml` 文件中增加站点然后在 Homestead 目录中执行 `vagrant provision`。

> **Note:** 这个操作是具有破坏性的，当执行 `provision` 命令，你现有的数据库会被摧毁并重新创建。

另外，也可以使用存放在 Homestead 环境中的 `serve` 命令文件。要使用 `serve` 命令文件，请先 SSH 进入 Homestead 环境中，并执行下列命令：

	serve domain.app /home/vagrant/Code/path/to/public/directory 80

> **附注：** 在执行 `serve` 命令过后，别忘记将新的站点加进本机的 `hosts` 文件中。

<a name="ports"></a>
## 连接端口

以下的端口将会被转发至 Homestead 环境：

- **SSH:** 2222 &rarr; Forwards To 22
- **HTTP:** 8000 &rarr; Forwards To 80
- **HTTPS:** 44300 &rarr; Forwards To 443
- **MySQL:** 33060 &rarr; Forwards To 3306
- **Postgres:** 54320 &rarr; Forwards To 5432

### 增加额外端口

你也可以自定义转发额外的端口至 Vagrant box，只需要指定协议：

	ports:
	    - send: 93000
	      to: 9300
	    - send: 7777
	      to: 777
	      protocol: udp

<a name="blackfire-profiler"></a>
## Blackfire Profiler

[Blackfire Profiler](https://blackfire.io) 是由 SensioLabs 创建的一个分析工具，它会自动的收集代码执行期间的相关数据，比如 RAM, CPU time, 和 disk I/O. 如果你使用 Homestead ，那么使用这个分析工具会变得非常简单。

blackfire 所需的包已经安装在 Homestead box 中，你只需要在 `Homestead.yaml` 文件中设置 **Server** ID 和 token ：

	blackfire:
	    - id: your-server-id
	      token: your-server-token
	      client-id: your-client-id
	      client-token: your-client-token

当你设定完 Blackfire 的凭证信息，使用 `vagrant provision` 令配置生效。当然，你也需要通过阅读[Blackfire 文档](https://blackfire.io/getting-started) 来学习如何在你的浏览器中安装 Blackfire 扩展。
