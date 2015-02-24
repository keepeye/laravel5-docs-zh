HTTP Controllers
================

-  `Introduction <#introduction>`__
-  `Basic Controllers <#basic-controllers>`__
-  `Controller Middleware <#controller-middleware>`__
-  `Implicit Controllers <#implicit-controllers>`__
-  `RESTful Resource Controllers <#restful-resource-controllers>`__
-  `Dependency Injection &
   Controllers <#dependency-injection-and-controllers>`__
-  `Route Caching <#route-caching>`__

 ## Introduction

Instead of defining all of your request handling logic in a single
``routes.php`` file, you may wish to organize this behavior using
Controller classes. Controllers can group related HTTP request handling
logic into a class. Controllers are typically stored in the
``app/Http/Controllers`` directory.

 ## Basic Controllers

Here is an example of a basic controller class:

::

    <?php namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;

    class UserController extends Controller {

        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return Response
         */
        public function showProfile($id)
        {
            return view('user.profile', ['user' => User::findOrFail($id)]);
        }

    }

We can route to the controller action like so:

::

    Route::get('user/{id}', 'UserController@showProfile');

    **Note:** All controllers should extend the base controller class.

Controllers & Namespaces
^^^^^^^^^^^^^^^^^^^^^^^^

It is very important to note that we did not need to specify the full
controller namespace, only the portion of the class name that comes
after the ``App\Http\Controllers`` namespace "root". By default, the
``RouteServiceProvider`` will load the ``routes.php`` file within a
route group containing the root controller namespace.

If you choose to nest or organize your controllers using PHP namespaces
deeper into the ``App\Http\Controllers`` directory, simply use the
specific class name relative to the ``App\Http\Controllers`` root
namespace. So, if your full controller class is
``App\Http\Controllers\Photos\AdminController``, you would register a
route like so:

::

    Route::get('foo', 'Photos\AdminController@method');

Naming Controller Routes
^^^^^^^^^^^^^^^^^^^^^^^^

Like Closure routes, you may specify names on controller routes:

::

    Route::get('foo', ['uses' => 'FooController@method', 'as' => 'name']);

URLs To Controller Actions
^^^^^^^^^^^^^^^^^^^^^^^^^^

To generate a URL to a controller action, use the ``action`` helper
method:

::

    $url = action('App\Http\Controllers\FooController@method');

If you wish to generate a URL to a controller action while using only
the portion of the class name relative to your controller namespace,
register the root controller namespace with the URL generator:

::

    URL::setRootControllerNamespace('App\Http\Controllers');

    $url = action('FooController@method');

You may access the name of the controller action being run using the
``currentRouteAction`` method:

::

    $action = Route::currentRouteAction();

 ## Controller Middleware

`Middleware </docs/5.0/middleware>`__ may be specified on controller
routes like so:

::

    Route::get('profile', [
        'middleware' => 'auth',
        'uses' => 'UserController@showProfile'
    ]);

Additionally, you may specify middleware within your controller's
constructor:

::

    class UserController extends Controller {

        /**
         * Instantiate a new UserController instance.
         */
        public function __construct()
        {
            $this->middleware('auth');

            $this->middleware('log', ['only' => ['fooAction', 'barAction']]);

            $this->middleware('subscribed', ['except' => ['fooAction', 'barAction']]);
        }

    }

 ## Implicit Controllers

Laravel allows you to easily define a single route to handle every
action in a controller. First, define the route using the
``Route::controller`` method:

::

    Route::controller('users', 'UserController');

The ``controller`` method accepts two arguments. The first is the base
URI the controller handles, while the second is the class name of the
controller. Next, just add methods to your controller, prefixed with the
HTTP verb they respond to:

::

    class UserController extends BaseController {

        public function getIndex()
        {
            //
        }

        public function postProfile()
        {
            //
        }

        public function anyLogin()
        {
            //
        }

    }

The ``index`` methods will respond to the root URI handled by the
controller, which, in this case, is ``users``.

If your controller action contains multiple words, you may access the
action using "dash" syntax in the URI. For example, the following
controller action on our ``UserController`` would respond to the
``users/admin-profile`` URI:

::

    public function getAdminProfile() {}

 ## RESTful Resource Controllers

Resource controllers make it painless to build RESTful controllers
around resources. For example, you may wish to create a controller that
handles HTTP requests regarding "photos" stored by your application.
Using the ``make:controller`` Artisan command, we can quickly create
such a controller:

::

    php artisan make:controller PhotoController

Next, we register a resourceful route to the controller:

::

    Route::resource('photo', 'PhotoController');

This single route declaration creates multiple routes to handle a
variety of RESTful actions on the photo resource. Likewise, the
generated controller will already have methods stubbed for each of these
actions, including notes informing you which URIs and verbs they handle.

Actions Handled By Resource Controller
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+-------------+-----------------------------+-----------+--------------------+
| Verb        | Path                        | Action    | Route Name         |
+=============+=============================+===========+====================+
| GET         | /resource                   | index     | resource.index     |
+-------------+-----------------------------+-----------+--------------------+
| GET         | /resource/create            | create    | resource.create    |
+-------------+-----------------------------+-----------+--------------------+
| POST        | /resource                   | store     | resource.store     |
+-------------+-----------------------------+-----------+--------------------+
| GET         | /resource/{resource}        | show      | resource.show      |
+-------------+-----------------------------+-----------+--------------------+
| GET         | /resource/{resource}/edit   | edit      | resource.edit      |
+-------------+-----------------------------+-----------+--------------------+
| PUT/PATCH   | /resource/{resource}        | update    | resource.update    |
+-------------+-----------------------------+-----------+--------------------+
| DELETE      | /resource/{resource}        | destroy   | resource.destroy   |
+-------------+-----------------------------+-----------+--------------------+

Customizing Resource Routes
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Additionally, you may specify only a subset of actions to handle on the
route:

::

    Route::resource('photo', 'PhotoController',
                    ['only' => ['index', 'show']]);

    Route::resource('photo', 'PhotoController',
                    ['except' => ['create', 'store', 'update', 'destroy']]);

By default, all resource controller actions have a route name; however,
you can override these names by passing a ``names`` array with your
options:

::

    Route::resource('photo', 'PhotoController',
                    ['names' => ['create' => 'photo.build']]);

Handling Nested Resource Controllers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To "nest" resource controllers, use "dot" notation in your route
declaration:

::

    Route::resource('photos.comments', 'PhotoCommentController');

This route will register a "nested" resource that may be accessed with
URLs like the following: ``photos/{photos}/comments/{comments}``.

::

    class PhotoCommentController extends Controller {

        /**
         * Show the specified photo comment.
         *
         * @param  int  $photoId
         * @param  int  $commentId
         * @return Response
         */
        public function show($photoId, $commentId)
        {
            //
        }

    }

Adding Additional Routes To Resource Controllers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If it becomes necessary to add additional routes to a resource
controller beyond the default resource routes, you should define those
routes before your call to ``Route::resource``:

::

    Route::get('photos/popular');

    Route::resource('photos', 'PhotoController');

 ## Dependency Injection & Controllers

Constructor Injection
^^^^^^^^^^^^^^^^^^^^^

The Laravel `service container </docs/5.0/container>`__ is used to
resolve all Laravel controllers. As a result, you are able to type-hint
any dependencies your controller may need in its constructor:

::

    <?php namespace App\Http\Controllers;

    use Illuminate\Routing\Controller;
    use App\Repositories\UserRepository;

    class UserController extends Controller {

        /**
         * The user repository instance.
         */
        protected $users;

        /**
         * Create a new controller instance.
         *
         * @param  UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            $this->users = $users;
        }

    }

Of course, you may also type-hint any `Laravel
contract </docs/5.0/contracts>`__. If the container can resolve it, you
can type-hint it.

Method Injection
^^^^^^^^^^^^^^^^

In addition to constructor injection, you may also type-hint
dependencies on your controller's methods. For example, let's type-hint
the ``Request`` instance on one of our methods:

::

    <?php namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Routing\Controller;

    class UserController extends Controller {

        /**
         * Store a new user.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            $name = $request->input('name');

            //
        }

    }

If your controller method is also expecting input from a route
parameter, simply list your route arguments after your other
dependencies:

::

    <?php namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Routing\Controller;

    class UserController extends Controller {

        /**
         * Store a new user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function update(Request $request, $id)
        {
            //
        }

    }

    **Note:** Method injection is fully compatible with `model
    binding </docs/5.0/routing#route-model-binding>`__. The container
    will intelligently determine which arguments are model bound and
    which arguments should be injected.

 ## Route Caching

If your application is exclusively using controller routes, you may take
advantage of Laravel's route cache. Using the route cache will
drastically decrease the amount of time it take to register all of your
application's routes. In some cases, your route registration may even be
up to 100x faster! To generate a route cache, just execute the
``route:cache`` Artisan command:

::

    php artisan route:cache

That's all there is to it! Your cached routes file will now be used
instead of your ``app/Http/routes.php`` file. Remember, if you add any
new routes you will need to generate a fresh route cache. Because of
this, you may wish to only run the ``route:cache`` command during your
project's deployment.

To remove the cached routes file without generating a new cache, use the
``route:clear`` command:

::

    php artisan route:clear

