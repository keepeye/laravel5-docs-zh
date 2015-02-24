Service Providers
=================

-  `Introduction <#introduction>`__
-  `Basic Provider Example <#basic-provider-example>`__
-  `Registering Providers <#registering-providers>`__
-  `Deferred Providers <#deferred-providers>`__

 ## Introduction

Service providers are the central place of all Laravel application
bootstrapping. Your own application, as well as all of Laravel's core
services are bootstrapped via service providers.

But, what do we mean by "bootstrapped"? In general, we mean
**registering** things, including registering service container
bindings, event listeners, filters, and even routes. Service providers
are the central place to configure your application.

If you open the ``config/app.php`` file included with Laravel, you will
see a ``providers`` array. These are all of the service provider classes
that will be loaded for your application. Of course, many of them are
"deferred" providers, meaning they will not be loaded on every request,
but only when the services they provide are actually needed.

In this overview you will learn how to write your own service providers
and register them with your Laravel application.

 ## Basic Provider Example

All service providers extend the ``Illuminate\Support\ServiceProvider``
class. This abstract class requires that you define at least one method
on your provider: ``register``. Within the ``register`` method, you
should **only bind things into the `service
container </docs/5.0/container>`__**. You should never attempt to
register any event listeners, routes, or any other piece of
functionality within the ``register`` method.

The Artisan CLI can easily generate a new provider via the
``make:provider`` command:

::

    php artisan make:provider RiakServiceProvider

The Register Method
~~~~~~~~~~~~~~~~~~~

Now, let's take a look at a basic service provider:

::

    <?php namespace App\Providers;

    use Riak\Connection;
    use Illuminate\Support\ServiceProvider;

    class RiakServiceProvider extends ServiceProvider {

        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            $this->app->singleton('Riak\Contracts\Connection', function($app)
            {
                return new Connection($app['config']['riak']);
            });
        }

    }

This service provider only defines a ``register`` method, and uses that
method to define an implementation of ``Riak\Contracts\Connection`` in
the service container. If you don't understand how the service container
works, don't worry, `we'll cover that soon </docs/5.0/container>`__.

This class is namespaced under ``App\Providers`` since that is the
default location for service providers in Laravel. However, you are free
to change this as you wish. Your service providers may be placed
anywhere that Composer can autoload them.

The Boot Method
~~~~~~~~~~~~~~~

So, what if we need to register an event listener within our service
provider? This should be done within the ``boot`` method. **This method
is called after all other service providers have been registered**,
meaning you have access to all other services that have been registered
by the framework.

::

    <?php namespace App\Providers;

    use Event;
    use Illuminate\Support\ServiceProvider;

    class EventServiceProvider extends ServiceProvider {

        /**
         * Perform post-registration booting of services.
         *
         * @return void
         */
        public function boot()
        {
            Event::listen('SomeEvent', 'SomeEventHandler');
        }

        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            //
        }

    }

We are able to type-hint dependencies for our ``boot`` method. The
service container will automatically inject any dependencies you need:

::

    use Illuminate\Contracts\Events\Dispatcher;

    public function boot(Dispatcher $events)
    {
        $events->listen('SomeEvent', 'SomeEventHandler');
    }

 ## Registering Providers

All service providers are registered in the ``config/app.php``
configuration file. This file contains a ``providers`` array where you
can list the names of your service providers. By default, a set of
Laravel core service providers are listed in this array. These providers
bootstrap the core Laravel components, such as the mailer, queue, cache,
and others.

To register your provider, simply add it to the array:

::

    'providers' => [
        // Other Service Providers

        'App\Providers\AppServiceProvider',
    ],

 ## Deferred Providers

If your provider is **only** registering bindings in the `service
container </docs/5.0/container>`__, you may choose to defer its
registration until one of the registered bindings is actually needed.
Deferring the loading of such a provider will improve the performance of
your application, since it is not loaded from the filesystem on every
request.

To defer the loading of a provider, set the ``defer`` property to
``true`` and define a ``provides`` method. The ``provides`` method
returns the service container bindings that the provider registers:

::

    <?php namespace App\Providers;

    use Riak\Connection;
    use Illuminate\Support\ServiceProvider;

    class RiakServiceProvider extends ServiceProvider {

        /**
         * Indicates if loading of the provider is deferred.
         *
         * @var bool
         */
        protected $defer = true;

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            $this->app->singleton('Riak\Contracts\Connection', function($app)
            {
                return new Connection($app['config']['riak']);
            });
        }

        /**
         * Get the services provided by the provider.
         *
         * @return array
         */
        public function provides()
        {
            return ['Riak\Contracts\Connection'];
        }

    }

Laravel compiles and stores a list of all of the services supplied by
deferred service providers, along with the name of its service provider
class. Then, only when you attempt to resolve one of these services does
Laravel load the service provider.
