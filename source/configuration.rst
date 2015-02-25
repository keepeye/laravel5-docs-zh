配置
###############

-  `导言`_
-  `安装之后`_
-  `获取配置项的值`_
-  `环境配置`_
-  `配置缓存`_
-  `维护模式`_
-  `漂亮的URL格式`_

导言
====

所有的配置文件放在 ``config`` 目录里.每个配置选项都有文档注释，可以自己过一遍，熟悉下这些配置项是很有用的。

安装之后
=======

命名你的应用
-------------

安装完Laravel之后，你可能想要为你的应用起个名字。默认来说， ``app`` 目录对应命名空间 ``App``，Composer使用 `PSR-4标准 <http://www.php-fig.org/psr/psr-4/>`__ 进行自动加载。当然，你可以通过Artisan命令 ``app:name`` 修改名字。

例如，如果你想要命名为 "Horsefly"，你可以在项目根目录下执行下面这条命令：

::

    php artisan app:name Horsefly

重命名你的应用完全是没有必要的，如果你愿意的话可以自由使用 ``App`` 这个名字。

别的配置
----------

Laravel几乎不需要额外的配置，你就可以自由的开始开发！当然，你可能想检查下 ``config/app.php`` 配置文件和它的注释文档。它包含了一些配置项，比如 ``timezone`` 和 ``locale`` 可能需要根据你的应用而改变。

当Laravel安装好以后，你还可以 :ref:`配置环境<configuration_env_config>`.

.. note::

    在生产环境下， ``app.debug`` 不应该设置为true，但本地环境下需要.

*Laravel还需要 ``storage`` 目录的可写权限。*

获取配置项的值
=============

你可以使用 ``Config`` 外观类很容易的获取配置值:

::

    $value = Config::get('app.timezone');

    Config::set('app.timezone', 'America/Chicago');

你也可以用 ``config`` 助手函数:

::

    $value = config('app.timezone');

.. _configuration_env_config:

环境配置
===========

根据应用运行环境使用不同的配置值通常是很有用的。比如，你希望本地和线上服务器用的不同的缓存驱动。通过环境配置很容易实现。

为了让这个功能更加容易实现，Laravel使用了Vance Lucas写的PHP类 `DotEnv <https://github.com/vlucas/phpdotenv>`__ 。在新安装的Laravel里，应用根目录包含一个文件 ``.env.example``。如果你是通过Composer安装的，这个文件会自动重命名为 ``.env`` ，否则你需要自己重命名它。

这个文件中列出来的所有变量会在你的应用接收到一个请求时自动加载到PHP全局变量 ``$_ENV`` 中。你可以使用助手函数 ``env`` 取回这些变量，实际上如果你检查Laravel的那些配置文件，你会发现一些选项已经使用了这个助手函数。

根据你自己的本地服务器环境需求随意修改环境变量。不管怎样你的 ``.env`` 文件不应当提交到版本库里，使用你的应用的每个开发者或服务器可能包含一个不同的环境配置。

如果你在一个团队中进行开发，你可能希望在应用中继续含有一个 ``.env.example`` 文件。通过在示例配置文件中预置一些值，队友就可以清楚的知道哪些环境变量是需要配置的。

获取当前的应用环境
-----------------

你可以通过 ``Application`` 实例的 ``environment`` 方法访问当前应用环境：

::

    $environment = $app->environment();


你还可以传参数给这个方法来检查是环境是否符合其中一个：

::

    if ($app->environment('local'))
    {
        // 是local环境
    }

    if ($app->environment('local', 'staging'))
    {
        // 是 local 或 staging...
    }


要获取application的实例，可以通过 :doc:`服务容器 <container>` 中的 ``Illuminate\Contracts\Foundation\Application`` contract。当然，如果在 :doc:`服务提供器 <providers>` 中，可以通过 ``$this->app`` 获取。

application实例还可以通过 ``app`` 助手函数和 ``App`` 外观类访问：

::

    $environment = app()->environment();

    $environment = App::environment();

配置缓存
=========

为了让应用启动稍微快一些，你可以使用 ``config:cache`` Artisan命令缓存所有配置文件到一个单独的文件中，单个文件可以更快的被框架加载。

在开发时你需要经常执行 ``config:cache`` 命令。

维护模式
=========

当你的应用处于维护模式，所有请求都将显示为一个自定义的试图。这使得你在升级或者维护时很容易"关闭"应用。维护模式的检查已经被包含在应用默认的中间件栈里了。如果应用处于维护模式，一个 ``HttpException`` 异常会伴随503状态码被抛出。

开启维护模式，只需要执行 ``down`` Artisan命令：

::

    php artisan down

相对的，关闭维护模式，使用 ``up`` 命令：

::

    php artisan up


维护模式响应模板
---------------

默认的维护模式响应视图的模板位于 ``resources/views/errors/503.blade.php`` 。

维护模式下的队列任务
-------------------

当你的应用处于维护模式下，任何 :doc:`队列任务<queues>` 都不会被处理。任务将在应用处于非维护模式时继续执行。

漂亮的URL格式
=============

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

当然, 使用 :doc:`Homestead <homestead>` 的话, 这个会自动配置好.