Laravel Homestead
=================

-  :ref:`homestead_introduction`
-  :ref:`homestead_included_softwares`
-  :ref:`homestead_intall`
-  :ref:`homestead_daily_usage`
-  :ref:`homestead_ports`

.. _homestead_introduction:

介绍
----

Laravel 致力于更加愉快的 PHP 开发体验，包括你的本地开发环境。 `Vagrant <http://vagrantup.com>`__ 提供了一个简单优雅的途径去管理和预备虚拟机。

Laravel Homestead 是一个官方的，预置的Vagrant Box，提供你一个完美的开发环境，不需要在你的本机端安装 PHP、HHVM、网页服务器或任何服务器软件。不用再担心搞乱你的系统！Vagrant Box是完全可丢弃的，如果有什么地方出现故障，你可以在几分钟内快速的销毁并重建虚拟机。

Homestead 可以在任何Windows、Mac或Linux系统上运行，并且自带Nginx、PHP 5.6、MySql、Postgres、Redis、Memcached和所有其他你需要用来开发牛逼Laravel应用的软件。

.. note::

    如果你用Windows系统，你可能需要允许 ``hardware virtualization (VT-x)`` 功能，通常可以在BIOS里开启。


Homestead 目前使用 Vagrant 1.6 构建并测试通过。

.. _homestead_included_softwares:

内置软件
--------

-  Ubuntu 14.04
-  PHP 5.6
-  HHVM
-  Nginx
-  MySQL
-  Postgres
-  Node (With Bower, Grunt, and Gulp)
-  Redis
-  Memcached
-  Beanstalkd
-  `Laravel Envoy </docs/ssh#envoy-task-runner>`__
-  Fabric + HipChat Extension

.. _homestead_intall:

安装&配置
---------

安装 VirtualBox 和 Vagrant
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在启动你的Homestead环境之前，你首先得安装 `VirtualBox <https://www.virtualbox.org/wiki/Downloads>`__ 和 `Vagrant <http://www.vagrantup.com/downloads.html>`__。这些软件在各平台都有提供简单的可视化安装程序。

添加 Vagrant Box
~~~~~~~~~~~~~~~~~~~~~~

一旦 VirtualBox 和 Vagrant 已经安装成功，你就可以使用下面的命令将 ``laravel/homestead`` 这个包添加到Vagrant中。可能需要花费几分钟下载这个box，这取决于你的网速：

::

    vagrant box add laravel/homestead

安装 Homestead
~~~~~~~~~~~~~~

**通过Git手动安装 (本地没有PHP)**


如果你不希望在你的本地安装PHP，你可以直接克隆项目手动安装。可以考虑克隆到home目录下的 ``Homestead`` 文件夹，这样 Homestead包 能为你的所有Laravel(和PHP)项目提供服务:

::

    git clone https://github.com/laravel/homestead.git Homestead

一旦你安装完成Homestead命令行工具，执行 ``bash init.sh`` 命令来创建 ``Homestead.yaml`` 配置文件:

::

    bash init.sh

``Homestead.yaml`` 文件会被放在 ``~/.homestead`` 目录下。


**使用Composer和PHP工具**

使用 Composer 的 ``global`` 命令:

::

    composer global require "laravel/homestead=~2.0"

确保 ``~/.composer/vendor/bin`` 在你的 PATH 环境变量中，以保证可以在终端执行 ``homestead`` 命令。

当你安装好 Homestead 命令行工具，运行 ``init`` 命令来创建 ``Homestead.yaml`` 配置文件:

::

    homestead init

``Homestead.yaml`` 文件会被放在 ``~/.homestead`` 目录下。如果你用的 Mac 或 Linux 系统，你可以在终端通过下面这个命令编辑这个文件:

::

    homestead edit

设置你的 SSH密钥
~~~~~~~~~~~~~~~~

下一步，你需要修改 ``Homestead.yaml`` 文件。这个文件里你可以配置你的 SSH 公钥路径，除此之外还有你想要在主机和虚拟机之间共享的文件夹。

没有SSH密钥？在 Mac 和 Linux中，你可以通过下面的命令生成一个SSH密钥对。

::

    ssh-keygen -t rsa -C "you@homestead"

在Windows系统下，你可以安装 `Git <http://git-scm.com/>`__ 然后使用Git附带的 ``Git Bash`` 工具来执行上面的命令。
或者, 你可以使用
`PuTTY <http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html>`__
和
`PuTTYgen <http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html>`__.

一旦你创建了一个SSH密钥，通过 ``Homestead.yaml`` 的 ``authorize`` 属性指定密钥的路径。

设置共享目录
~~~~~~~~~~~

``Homestead.yaml`` 文件里的 ``folders`` 属性列出所有你想要共享给 Homestead 环境的目录。如果这些目录里的文件变化了，他们会自动在本地和Homestead环境中保持同步。你可以添加任意多需要共享的目录。

配置 Nginx 站点
~~~~~~~~~~~~~~~~~~~~~~~~~~

不熟悉 Nginx？没事儿。 ``sites`` 属性使你可以简单的绑定一个域名到一个目录。一个简单的站点配置已经包含在 ``Homestead.yaml`` 里了。同样的，你可以加任何你需要的站点到你的 Homestead 环境中。Homestead 可以为你每个进行中的 Laravel 应用提供方便的虚拟化环境。

你可以通过设置 ``hhvm`` 选项为 ``true`` 使你所有Homestead里的站点支持 `HHVM <http://hhvm.com>`__。

::

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
          hhvm: true

命令别名
~~~~~~~

添加命令别名，只需要写到 ``~/.homestead`` 根路径下的 ``aliases`` 文件里即可。

启动 Vagrant Box
~~~~~~~~~~~~~~~~~~~~~~

一旦你改好 ``Homestead.yaml`` ，在Homestead目录下执行 ``vagrant up`` 命令。

Vagrant 会将虚拟机开机，并且自动配置你的共享目录和 Nginx 站点。如果要移除虚拟机，可以使用 ``vagrant destroy --force`` 命令。

为了你的 Nginx 站点，别忘记在你的机器的 hosts 文件将域名加进去。``hosts`` 文件会将你的本地域名的站点请求重导至你的 Homestead 环境中。在 Mac 和 Linux，该文件放在 ``/etc/hosts`` 。在 Windows 环境中，它被放置在 ``C:\Windows\System32\drivers\etc\hosts``。你要加进去的内容类似如下：

::

    192.168.10.10  homestead.app

务必确认 IP 位置与你的 ``Homestead.yaml`` 文件中的相同。一旦你将域名加进你的 ``hosts`` 文件中，你就可以通过网页浏览器访问到你的站点！

::

    http://homestead.app

想要学习如何连接数据库，请继续往下看！

.. _homestead_daily_usage:

常见用法
--------

通过SSH连接
~~~~~~~~~~~~

要通过 SSH 连接上您的 Homestead 环境，在终端机里进入你的 Homestead 目录并执行 ``vagrant ssh`` 命令。

因为你可能会经常需要透过 SSH 进入你的 Homestead 虚拟机，可以考虑在你的主要机器上创建一个"别名":

::

    alias vm="ssh vagrant@127.0.0.1 -p 2222"

一旦你创建了这个别名，无论你在主要机器的哪个目录，都可以简单地使用 "vm" 命令来通过 SSH 进入你的 Homestead 虚拟机。

连接到你的数据库
~~~~~~~~~~~~~~~~~

在 ``Homestead`` 封装包中，MySQL 与 Postgres 两套数据库都已经预装。为了更简便，Laravel 的 ``local`` 数据库配置已经默认将其配置完成。

如果想要从本机上通过 Navicat 或者是 Sequel Pro 连接 MySQL 或者 Postgres 数据库，你可以连接 ``127.0.0.1``` 的端口 33060 (MySQL) 或 54320 (Postgres)。而帐号密码分别是 ``homestead`` / ``secret``。

.. note::

    从本机端你应该只能使用这些非标准的连接端口来连接数据库。因为当 Laravel 运行在虚拟机时，在 Laravel 的数据库配置文件中依然是配置使用默认的 3306 及 5432 连接端口。

添加更多的站点
~~~~~~~~~~~~~~

一旦 Homestead 环境上架且运行后，你可能会需要为 Laravel 应用程序增加更多的 Nginx 站点。你可以在单一个 Homestead 环境中运行非常多 Laravel 安装程序。有两种方式可以达成：第一种，在 ``Homestead.yaml ``文件中增加站点然后执行 ``vagrant provision``。

另外，也可以使用存放在 Homestead 环境中的 ``serve`` 命令文件。要使用 ``serve`` 命令文件，请先 SSH 进入 Homestead 环境中，并执行下列命令：

::

    serve domain.app /home/vagrant/Code/path/to/public/directory

.. note::

    在执行 ``serve`` 命令过后，别忘记将新的站点加进本机的 ``hosts`` 文件中。

.. _homestead_ports:

端口
----

以下的端口将会被转发至 Homestead 环境：

-  **SSH:** 2222 → Forwards To 22
-  **HTTP:** 8000 → Forwards To 80
-  **MySQL:** 33060 → Forwards To 3306
-  **Postgres:** 54320 → Forwards To 5432

**增加额外端口**

你也可以自定义转发额外的端口至 Vagrant box，只需要指定协议：

::

    ports:
        - send: 93000
          to: 9300
        - send: 7777
          to: 777
          protocol: udp
