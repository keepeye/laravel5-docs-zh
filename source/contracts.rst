Contracts
=========

-  `Introduction <#introduction>`__
-  `Why Contracts? <#why-contracts>`__
-  `Contract Reference <#contract-reference>`__
-  `How To Use Contracts <#how-to-use-contracts>`__

 ## Introduction

Laravel's Contracts are a set of interfaces that define the core
services provided by the framework. For example, a ``Queue`` contract
defines the methods needed for queueing jobs, while the ``Mailer``
contract defines the methods needed for sending e-mail.

Each contract has a corresponding implementation provided by the
framework. For example, Laravel provides a ``Queue`` implementation with
a variety of drivers, and a ``Mailer`` implementation that is powered by
`SwiftMailer <http://swiftmailer.org/>`__.

All of the Laravel contracts live in `their own GitHub
repository <https://github.com/illuminate/contracts>`__. This provides a
quick reference point for all available contracts, as well as a single,
decoupled package that may be utilized by other package developers.

 ## Why Contracts?

You may have several questions regarding contracts. Why use interfaces
at all? Isn't using interfaces more complicated?

Let's distill the reasons for using interfaces to the following
headings: loose coupling and simplicity.

Loose Coupling
~~~~~~~~~~~~~~

First, let's review some code that is tightly coupled to a cache
implementation. Consider the following:

::

    <?php namespace App\Orders;

    class Repository {

        /**
         * The cache.
         */
        protected $cache;

        /**
         * Create a new repository instance.
         *
         * @param  \Package\Cache\Memcached  $cache
         * @return void
         */
        public function __construct(\SomePackage\Cache\Memcached $cache)
        {
            $this->cache = $cache;
        }

        /**
         * Retrieve an Order by ID.
         *
         * @param  int  $id
         * @return Order
         */
        public function find($id)
        {
            if ($this->cache->has($id))
            {
                //
            }
        }

    }

In this class, the code is tightly coupled to a given cache
implementation. It is tightly coupled because we are depending on a
concrete Cache class from a package vendor. If the API of that package
changes our code must change as well.

Likewise, if we want to replace our underlying cache technology
(Memcached) with another technology (Redis), we again will have to
modify our repository. Our repository should not have so much knowledge
regarding who is providing them data or how they are providing it.

**Instead of this approach, we can improve our code by depending on a
simple, vendor agnostic interface:**

::

    <?php namespace App\Orders;

    use Illuminate\Contracts\Cache\Repository as Cache;

    class Repository {

        /**
         * Create a new repository instance.
         *
         * @param  Cache  $cache
         * @return void
         */
        public function __construct(Cache $cache)
        {
            $this->cache = $cache;
        }

    }

Now the code is not coupled to any specific vendor, or even Laravel.
Since the contracts package contains no implementation and no
dependencies, you may easily write an alternative implementation of any
given contract, allowing you to replace your cache implementation
without modifying any of your cache consuming code.

Simplicity
~~~~~~~~~~

When all of Laravel's services are neatly defined within simple
interfaces, it is very easy to determine the functionality offered by a
given service. **The contracts serve as succinct documentation to the
framework's features.**

In addition, when you depend on simple interfaces, your code is easier
to understand and maintain. Rather than tracking down which methods are
available to you within a large, complicated class, you can refer to a
simple, clean interface.

 ## Contract Reference

This is a reference to most Laravel Contracts, as well as their Laravel
"facade" counterparts:

+----------------------------------------------------------------------------------------------------+----------------------+
| Contract                                                                                           | Laravel 4.x Facade   |
+====================================================================================================+======================+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Auth/Guard.php>`__                | Auth                 |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Auth/PasswordBroker.php>`__       | Password             |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Cache/Repository.php>`__          | Cache                |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Cache/Factory.php>`__             | Cache::driver()      |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Config/Repository.php>`__         | Config               |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Container/Container.php>`__       | App                  |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Cookie/Factory.php>`__            | Cookie               |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Cookie/QueueingFactory.php>`__    | Cookie::queue()      |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Encryption/Encrypter.php>`__      | Crypt                |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Events/Dispatcher.php>`__         | Event                |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Filesystem/Cloud.php>`__          |                      |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Filesystem/Factory.php>`__        | File                 |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Filesystem/Filesystem.php>`__     | File                 |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Foundation/Application.php>`__    | App                  |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Hashing/Hasher.php>`__            | Hash                 |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Logging/Log.php>`__               | Log                  |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Mail/MailQueue.php>`__            | Mail::queue()        |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Mail/Mailer.php>`__               | Mail                 |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Queue/Factory.php>`__             | Queue::driver()      |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Queue/Queue.php>`__               | Queue                |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Redis/Database.php>`__            | Redis                |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Routing/Registrar.php>`__         | Route                |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Routing/ResponseFactory.php>`__   | Response             |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Routing/UrlGenerator.php>`__      | URL                  |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Support/Arrayable.php>`__         |                      |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Support/Jsonable.php>`__          |                      |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Support/Renderable.php>`__        |                      |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Validation/Factory.php>`__        | Validator::make()    |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/Validation/Validator.php>`__      |                      |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/View/Factory.php>`__              | View::make()         |
+----------------------------------------------------------------------------------------------------+----------------------+
| `Illuminate <https://github.com/illuminate/contracts/blob/master/View/View.php>`__                 |                      |
+----------------------------------------------------------------------------------------------------+----------------------+

 ## How To Use Contracts

So, how do you get an implementation of a contract? It's actually quite
simple. Many types of classes in Laravel are resolved through the
`service container </docs/5.0/container>`__, including controllers,
event listeners, filters, queue jobs, and even route Closures. So, to
get an implementation of a contract, you can just "type-hint" the
interface in the constructor of the class being resolved. For example,
take a look at this event handler:

::

    <?php namespace App\Handlers\Events;

    use App\User;
    use App\Events\NewUserRegistered;
    use Illuminate\Contracts\Redis\Database;

    class CacheUserInformation {

        /**
         * The Redis database implementation.
         */
        protected $redis;

        /**
         * Create a new event handler instance.
         *
         * @param  Database  $redis
         * @return void
         */
        public function __construct(Database $redis)
        {
            $this->redis = $redis;
        }

        /**
         * Handle the event.
         *
         * @param  NewUserRegistered  $event
         * @return void
         */
        public function handle(NewUserRegistered $event)
        {
            //
        }

    }

When the event listener is resolved, the service container will read the
type-hints on the constructor of the class, and inject the appropriate
value. To learn more about registering things in the service container,
check out `the documentation </docs/5.0/container>`__.
