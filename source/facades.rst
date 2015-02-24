Facades
=======

-  `Introduction <#introduction>`__
-  `Explanation <#explanation>`__
-  `Practical Usage <#practical-usage>`__
-  `Creating Facades <#creating-facades>`__
-  `Mocking Facades <#mocking-facades>`__
-  `Facade Class Reference <#facade-class-reference>`__

 ## Introduction

Facades provide a "static" interface to classes that are available in
the application's `IoC container </docs/5.0/container>`__. Laravel ships
with many facades, and you have probably been using them without even
knowing it! Laravel "facades" serve as "static proxies" to underlying
classes in the IoC container, providing the benefit of a terse,
expressive syntax while maintaining more testability and flexibility
than traditional static methods.

Occasionally, You may wish to create your own facades for your
applications and packages, so let's explore the concept, development and
usage of these classes.

    **Note:** Before digging into facades, it is strongly recommended
    that you become very familiar with the Laravel `IoC
    container </docs/5.0/container>`__.

 ## Explanation

In the context of a Laravel application, a facade is a class that
provides access to an object from the container. The machinery that
makes this work is in the ``Facade`` class. Laravel's facades, and any
custom facades you create, will extend the base ``Facade`` class.

Your facade class only needs to implement a single method:
``getFacadeAccessor``. It's the ``getFacadeAccessor`` method's job to
define what to resolve from the container. The ``Facade`` base class
makes use of the ``__callStatic()`` magic-method to defer calls from
your facade to the resolved object.

So, when you make a facade call like ``Cache::get``, Laravel resolves
the Cache manager class out of the IoC container and calls the ``get``
method on the class. In technical terms, Laravel Facades are a
convenient syntax for using the Laravel IoC container as a service
locator.

 ## Practical Usage

In the example below, a call is made to the Laravel cache system. By
glancing at this code, one might assume that the static method ``get``
is being called on the ``Cache`` class.

::

    $value = Cache::get('key');

However, if we look at that ``Illuminate\Support\Facades\Cache`` class,
you'll see that there is no static method ``get``:

::

    class Cache extends Facade {

        /**
         * Get the registered name of the component.
         *
         * @return string
         */
        protected static function getFacadeAccessor() { return 'cache'; }

    }

The Cache class extends the base ``Facade`` class and defines a method
``getFacadeAccessor()``. Remember, this method's job is to return the
name of an IoC binding.

When a user references any static method on the ``Cache`` facade,
Laravel resolves the ``cache`` binding from the IoC container and runs
the requested method (in this case, ``get``) against that object.

So, our ``Cache::get`` call could be re-written like so:

::

    $value = $app->make('cache')->get('key');

Importing Facades
^^^^^^^^^^^^^^^^^

Remember, if you are using a facade when a controller that is
namespaced, you will need to import the facade class into the namespace.
All facades live in the global namespace:

::

    <?php namespace App\Http\Controllers;

    use Cache;

    class PhotosController extends Controller {

        /**
         * Get all of the application photos.
         *
         * @return Response
         */
        public function index()
        {
            $photos = Cache::get('photos');

            //
        }

    }

 ## Creating Facades

Creating a facade for your own application or package is simple. You
only need 3 things:

-  An IoC binding.
-  A facade class.
-  A facade alias configuration.

Let's look at an example. Here, we have a class defined as
``PaymentGateway\Payment``.

::

    namespace PaymentGateway;

    class Payment {

        public function process()
        {
            //
        }

    }

We need to be able to resolve this class from the IoC container. So,
let's add a binding to a service provider:

::

    App::bind('payment', function()
    {
        return new \PaymentGateway\Payment;
    });

A great place to register this binding would be to create a new `service
provider </docs/5.0/container#service-providers>`__ named
``PaymentServiceProvider``, and add this binding to the ``register``
method. You can then configure Laravel to load your service provider
from the ``config/app.php`` configuration file.

Next, we can create our own facade class:

::

    use Illuminate\Support\Facades\Facade;

    class Payment extends Facade {

        protected static function getFacadeAccessor() { return 'payment'; }

    }

Finally, if we wish, we can add an alias for our facade to the
``aliases`` array in the ``config/app.php`` configuration file. Now, we
can call the ``process`` method on an instance of the ``Payment`` class.

::

    Payment::process();

A Note On Auto-Loading Aliases
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Classes in the ``aliases`` array are not available in some instances
because `PHP will not attempt to autoload undefined type-hinted
classes <https://bugs.php.net/bug.php?id=39003>`__. If
``\ServiceWrapper\ApiTimeoutException`` is aliased to
``ApiTimeoutException``, a ``catch(ApiTimeoutException $e)`` outside of
the namespace ``\ServiceWrapper`` will never catch the exception, even
if one is thrown. A similar problem is found in classes which have type
hints to aliased classes. The only workaround is to forego aliasing and
``use`` the classes you wish to type hint at the top of each file which
requires them.

 ## Mocking Facades

Unit testing is an important aspect of why facades work the way that
they do. In fact, testability is the primary reason for facades to even
exist. For more information, check out the `mocking
facades </docs/testing#mocking-facades>`__ section of the documentation.

 ## Facade Class Reference

Below you will find every facade and its underlying class. This is a
useful tool for quickly digging into the API documentation for a given
facade root. The `IoC binding </docs/5.0/container>`__ key is also
included where applicable.

+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Facade                 | Class                                                                                       | IoC Binding          |
+========================+=============================================================================================+======================+
| App                    | `Illuminate <http://laravel.com/api/5.0/Illuminate/Foundation/Application.html>`__          | ``app``              |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Artisan                | `Illuminate <http://laravel.com/api/5.0/Illuminate/Console/Application.html>`__             | ``artisan``          |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Auth                   | `Illuminate <http://laravel.com/api/5.0/Illuminate/Auth/AuthManager.html>`__                | ``auth``             |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Auth (Instance)        | `Illuminate <http://laravel.com/api/5.0/Illuminate/Auth/Guard.html>`__                      |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Blade                  | `Illuminate <http://laravel.com/api/5.0/Illuminate/View/Compilers/BladeCompiler.html>`__    | ``blade.compiler``   |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Cache                  | `Illuminate <http://laravel.com/api/5.0/Illuminate/Cache/Repository.html>`__                | ``cache``            |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Config                 | `Illuminate <http://laravel.com/api/5.0/Illuminate/Config/Repository.html>`__               | ``config``           |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Cookie                 | `Illuminate <http://laravel.com/api/5.0/Illuminate/Cookie/CookieJar.html>`__                | ``cookie``           |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Crypt                  | `Illuminate <http://laravel.com/api/5.0/Illuminate/Encryption/Encrypter.html>`__            | ``encrypter``        |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| DB                     | `Illuminate <http://laravel.com/api/5.0/Illuminate/Database/DatabaseManager.html>`__        | ``db``               |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| DB (Instance)          | `Illuminate <http://laravel.com/api/5.0/Illuminate/Database/Connection.html>`__             |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Event                  | `Illuminate <http://laravel.com/api/5.0/Illuminate/Events/Dispatcher.html>`__               | ``events``           |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| File                   | `Illuminate <http://laravel.com/api/5.0/Illuminate/Filesystem/Filesystem.html>`__           | ``files``            |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Form                   | `Illuminate <http://laravel.com/api/5.0/Illuminate/Html/FormBuilder.html>`__                | ``form``             |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Hash                   | `Illuminate <http://laravel.com/api/5.0/Illuminate/Hashing/HasherInterface.html>`__         | ``hash``             |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| HTML                   | `Illuminate <http://laravel.com/api/5.0/Illuminate/Html/HtmlBuilder.html>`__                | ``html``             |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Input                  | `Illuminate <http://laravel.com/api/5.0/Illuminate/Http/Request.html>`__                    | ``request``          |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Lang                   | `Illuminate <http://laravel.com/api/5.0/Illuminate/Translation/Translator.html>`__          | ``translator``       |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Log                    | `Illuminate <http://laravel.com/api/5.0/Illuminate/Log/Writer.html>`__                      | ``log``              |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Mail                   | `Illuminate <http://laravel.com/api/5.0/Illuminate/Mail/Mailer.html>`__                     | ``mailer``           |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Paginator              | `Illuminate <http://laravel.com/api/5.0/Illuminate/Pagination/Factory.html>`__              | ``paginator``        |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Paginator (Instance)   | `Illuminate <http://laravel.com/api/5.0/Illuminate/Pagination/Paginator.html>`__            |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Password               | `Illuminate <http://laravel.com/api/5.0/Illuminate/Auth/Passwords/PasswordBroker.html>`__   | ``auth.reminder``    |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Queue                  | `Illuminate <http://laravel.com/api/5.0/Illuminate/Queue/QueueManager.html>`__              | ``queue``            |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Queue (Instance)       | `Illuminate <http://laravel.com/api/5.0/Illuminate/Queue/QueueInterface.html>`__            |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Queue (Base Class)     | `Illuminate <http://laravel.com/api/5.0/Illuminate/Queue/Queue.html>`__                     |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Redirect               | `Illuminate <http://laravel.com/api/5.0/Illuminate/Routing/Redirector.html>`__              | ``redirect``         |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Redis                  | `Illuminate <http://laravel.com/api/5.0/Illuminate/Redis/Database.html>`__                  | ``redis``            |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Request                | `Illuminate <http://laravel.com/api/5.0/Illuminate/Http/Request.html>`__                    | ``request``          |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Response               | `Illuminate <http://laravel.com/api/5.0/Illuminate/Support/Facades/Response.html>`__        |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Route                  | `Illuminate <http://laravel.com/api/5.0/Illuminate/Routing/Router.html>`__                  | ``router``           |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Schema                 | `Illuminate <http://laravel.com/api/5.0/Illuminate/Database/Schema/Blueprint.html>`__       |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Session                | `Illuminate <http://laravel.com/api/5.0/Illuminate/Session/SessionManager.html>`__          | ``session``          |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Session (Instance)     | `Illuminate <http://laravel.com/api/5.0/Illuminate/Session/Store.html>`__                   |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| SSH                    | `Illuminate <http://laravel.com/api/5.0/Illuminate/Remote/RemoteManager.html>`__            | ``remote``           |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| SSH (Instance)         | `Illuminate <http://laravel.com/api/5.0/Illuminate/Remote/Connection.html>`__               |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| URL                    | `Illuminate <http://laravel.com/api/5.0/Illuminate/Routing/UrlGenerator.html>`__            | ``url``              |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Validator              | `Illuminate <http://laravel.com/api/5.0/Illuminate/Validation/Factory.html>`__              | ``validator``        |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| Validator (Instance)   | `Illuminate <http://laravel.com/api/5.0/Illuminate/Validation/Validator.html>`__            |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| View                   | `Illuminate <http://laravel.com/api/5.0/Illuminate/View/Factory.html>`__                    | ``view``             |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+
| View (Instance)        | `Illuminate <http://laravel.com/api/5.0/Illuminate/View/View.html>`__                       |
+------------------------+---------------------------------------------------------------------------------------------+----------------------+

