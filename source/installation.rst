安装laravel5
############

-  `安装 Composer <#install-composer>`__
-  `安装 Laravel <#install-laravel>`__
-  `环境要求 <#server-requirements>`__

安装 Composer
=============

Laravel 通过 `Composer <http://getcomposer.org>`__ 管理依赖. 
在安装Laravel前，请先了解一下Composer。

安装laravel
================

**通过 Laravel 安装器安装**


首先使用composer下载Laravel installer.

::

    composer global require "laravel/installer=~1.1"

请确保 ``~/.composer/vendor/bin`` 目录在你的系统环境变量中，以便``laravel``命令可被系统调用。


当安装成功后，通过简单的 ``laravel new`` 命令即可在你指定的位置创建一个新的laravel项目。例如，
``laravel new blog`` 命令可以创建一个名为 ``blog`` 的目录，里面是一个新的已经安装好所有依赖的laravel项目。
这种方式比通过Composer安装快得多:

::

    laravel new blog

**通过 Composer Create-Project 方式安装**


你仍然可以通过composer的 ``create-project`` 指令创建一个新的laravel项目，命令如下：


::

    composer create-project laravel/laravel --prefer-dist

环境要求
===========

-  PHP版本 >= 5.4
-  Mcrypt 扩展
-  OpenSSL 扩展
-  Mbstring 扩展

截至 PHP 5.5, 一些系统可能要求你自己安装 PHP JSON 扩展. 如果是ubuntu系统, 可以通过命令
``apt-get install php5-json`` 安装.

配置
----

安装完Laravel你需要做的第一件事是设置你的application key为一个随机的字符串。如果你是通过Composer安装的，这个key可能已经设置好了。

通常，这个key应该是一个32字节的字符串，可以在 ``.env`` 环境配置文件中设置。
**如果这个key没被设置，你的用户sessions和其他的需要加密的数据会不安全。**

Laravel几乎不需要额外的配置，你就可以自由的开始开发！当然，你可能想检查下 ``config/app.php`` 配置文件和它的注释文档。它包含了一些配置项，比如 ``timezone`` 和 ``locale`` 可能需要根据你的应用而改变。

当Laravel安装好以后，你还可以 `配置本地环境 </docs/5.0/configuration#environment-configuration>`__.

    **注意:** 在生产环境下， ``app.debug`` 不应该设置为true，但本地环境下需要.

*Laravel还需要 ``storage`` 目录的可写权限。*

漂亮的URL
---------

**Apache**

首先保证你的Apache ``mod_rewrite`` 模块已经开启.
框架默认有一个 ``public/.htaccess`` 文件可以实现隐藏 ``index.php`` 的访问.

如果无效的话，可以试试下面的规则:

::

    Options +FollowSymLinks
    RewriteEngine On

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

**Nginx**

在Nginx配置文件中通过下面的指令实现隐藏 ``index.php``:

::

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

当然, 使用 `Homestead </docs/5.0/homestead>`__ 的话, 这个会自动配置好.
