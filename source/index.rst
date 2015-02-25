
laravel5 中文手册
===================================

申明
-------

本文档为本人从 `Laravel官网 <http://laravel.com>`__ 尝试翻译而来。

翻译过程中还参考了 `<http://laravel-china.org/docs/5.0/>`__ 

文档源文件是根据官方提供的markdown文件转换成rst格式，再使用sphinx生成html格式在线阅读。

我已在github开了一个项目: `点击查看 <https://github.com/keepeye/laravel5-docs-zh>`__ ，你可以克隆项目，自己本地构建html离线文档～

交流
----

尽量翻译，能力所限难免有错误，为了方便交流，我对sphinx默认模板进行了一些修改，在每个页面下方都有留言框，欢迎使用。

需要本地构建的同学请先将 ``source/_templates`` 目录下的文件删除。

导航
--------

- `环境配置`_
- `基本功能`_
- `系统架构`_
- `系统服务`_
- `数据库`_
- `Artisan命令行工具`_

环境配置
^^^^^^^^
.. toctree::
   :maxdepth: 2

   安装<installation>
   配置<configuration>
   Homestead<homestead>

基本功能
^^^^^^^^
.. toctree::
   :maxdepth: 2

   路由<routing>
   中间件<middleware>
   控制器<controllers>
   请求<requests>
   响应<responses>
   视图<views>

系统架构
^^^^^^^^
.. toctree::
   :maxdepth: 2

   服务提供者<providers>
   服务容器<container>
   Contracts<contracts>
   外观模式<facades>
   请求生命周期<lifecycle>
   应用程序结构<structure>

系统服务
^^^^^^^^
.. toctree::
   :maxdepth: 2

   用户认证<authentication>
   交易<billing>
   缓存<cache>
   集合<collections>
   命令Bus<bus>
   核心扩展<extending>
   Elixir<elixir>
   加密<encryption>
   错误&日志<errors>
   事件<events>
   文件系统/云存储<filesystem>
   哈希<hashing>
   助手函数<helpers>
   本地化<localization>
   邮件<mail>
   扩展包开发<packages>
   分页<pagination>
   队列<queue>
   会话Session<session>
   模板<templates>
   单元测试<testing>
   验证<validation>

数据库
^^^^^^^^
.. toctree::
   :maxdepth: 2

   基础用法<database>
   查询构造器<queries>
   Eloquent ORM<eloquent>
   结构生成器<schema>
   迁移 & 数据填充<migrations>
   Redis<redis>

Artisan命令行工具
^^^^^^^^
.. toctree::
   :maxdepth: 2

   概览<artisan>
   开发<commands>
