Laravel Elixir
==============

-  `Introduction <#introduction>`__
-  `Installation & Setup <#installation>`__
-  `Usage <#usage>`__
-  `Gulp <#gulp>`__
-  `Extensions <#extensions>`__

 ## Introduction

Laravel Elixir provides a clean, fluent API for defining basic
`Gulp <http://gulpjs.com>`__ tasks for your Laravel application. Elixir
supports several common CSS and JavaScript pre-processors, and even
testing tools.

If you've ever been confused about how to get started with Gulp and
asset compilation, you will love Laravel Elixir!

 ## Installation & Setup

Installing Node
~~~~~~~~~~~~~~~

Before triggering Elixir, you must first ensure that Node.js is
installed on your machine.

::

    node -v

By default, Laravel Homestead includes everything you need; however, if
you aren't using Vagrant, then you can easily install Node by visiting
`their download page <http://nodejs.org/download/>`__. Don't worry, it's
quick and easy!

Gulp
~~~~

Next, you'll want to pull in `Gulp <http://gulpjs.com>`__ as a global
NPM package like so:

::

    npm install --global gulp

Laravel Elixir
~~~~~~~~~~~~~~

The only remaining step is to install Elixir! With a new install of
Laravel, you'll find a ``package.json`` file in the root. Think of this
like your ``composer.json`` file, except it defines Node dependencies
instead of PHP. You may install the dependencies it references by
running:

::

    npm install

 ## Usage

Now that you've installed Elixir, you'll be compiling and concatenating
in no time!

Compile Less
^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.less("app.less");
    });

In the example above, Elixir assumes that your Less files are stored in
``resources/assets/less``.

Compile Sass
^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.sass("app.sass");
    });

This assumes that your Sass files are stored in
``resources/assets/sass``.

Compile CoffeeScript
^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.coffee();
    });

This assumes that your CoffeeScript files are stored in
``resources/assets/coffee``.

Compile All Less and CoffeeScript
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.less()
           .coffee();
    });

Trigger PHPUnit Tests
^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.phpUnit();
    });

Trigger PHPSpec Tests
^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.phpSpec();
    });

Combine Stylesheets
^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.styles([
            "normalize.css",
            "main.css"
        ]);
    });

Paths passed to this method are relative to the ``resources/css``
directory.

Combine Stylesheets and Save to a Custom Directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.styles([
            "normalize.css",
            "main.css"
        ], 'public/build/css/everything.css');
    });

Combine Stylesheets From A Custom Base Directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.styles([
            "normalize.css",
            "main.css"
        ], 'public/build/css/everything.css', 'public/css');
    });

The third argument to both the ``styles`` and ``scripts`` methods
determines the relative directory for all paths passed to the methods.

Combine All Styles in a Directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.stylesIn("public/css");
    });

Combine Scripts
^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.scripts([
            "jquery.js",
            "app.js"
        ]);
    });

Again, this assumes all paths are relative to the ``resources/js``
directory.

Combine All Scripts in a Directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.scriptsIn("public/js/some/directory");
    });

Combine Multiple Sets of Scripts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.scripts(['jquery.js', 'main.js'], 'public/js/main.js')
           .scripts(['forum.js', 'threads.js'], 'public/js/forum.js');
    });

Version / Hash A File
^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.version("css/all.css");
    });

This will append a unique hash to the filename, allowing for
cache-busting. For example, the generated file name will look something
like: ``all-16d570a7.css``.

Within your views, you may use the ``elixir()`` function to load the
appropriately hashed asset. Here's an example:

.. code:: html

    <link rel="stylesheet" href="{{ elixir("css/all.css") }}">

Behind the scenes, the ``elixir()`` function will determine the name of
the hashed file that should be included. Don't you feel the weight
lifting off your shoulders already?

Copy a File to a New Location
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.copy('vendor/foo/bar.css', 'public/css/bar.css');
    });

Copy an Entire Directory to a New Location
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: javascript

    elixir(function(mix) {
        mix.copy('vendor/package/views', 'resources/views');
    });

Method Chaining
^^^^^^^^^^^^^^^

Of course, you may chain almost all of Elixir's methods together to
build your recipe:

.. code:: javascript

    elixir(function(mix) {
        mix.less("app.less")
           .coffee()
           .phpUnit()
           .version("css/bootstrap.css");
    });

 ## Gulp

Now that you've told Elixir which tasks to execute, you only need to
trigger Gulp from the command line.

Execute All Registered Tasks Once
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    gulp

Watch Assets For Changes
^^^^^^^^^^^^^^^^^^^^^^^^

::

    gulp watch

Watch Tests And PHP Classes for Changes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    gulp tdd

    **Note:** All tasks will assume a development environment, and will
    exclude minification. For production, use ``gulp --production``.

 ## Extensions

You can even create your own Gulp tasks, and hook them into Elixir.
Imagine that you want to add a fun task that uses the Terminal to
verbally notify you with some message. Here's what that might look like:

.. code:: javascript

     var gulp = require("gulp");
     var shell = require("gulp-shell");
     var elixir = require("laravel-elixir");

     elixir.extend("message", function(message) {

         gulp.task("say", function() {
             gulp.src("").pipe(shell("say " + message));
         });

         return this.queueTask("say");

     });

Notice that we ``extend`` Elixir's API by passing the key that we will
use within our Gulpfile, as well as a callback function that will create
the Gulp task.

If you want your custom task to be monitored, then register a watcher as
well.

.. code:: javascript

    this.registerWatcher("message", "**/*.php");

This lines designates that when any file that matches the regex,
``**/*.php`` is modified, we want to trigger the ``message`` task.

That's it! You may either place this at the top of your Gulpfile, or
instead extract it to a custom tasks file. If you choose the latter
approach, simply require it into your Gulpfile, like so:

.. code:: javascript

    require("./custom-tasks")

You're done! Now, you can mix it in.

.. code:: javascript

    elixir(function(mix) {
        mix.message("Tea, Earl Grey, Hot");
    });

With this addition, each time you trigger Gulp, Picard will request some
tea.
