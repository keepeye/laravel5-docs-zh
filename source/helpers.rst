Helper Functions
================

-  `Arrays <#arrays>`__
-  `Paths <#paths>`__
-  `Strings <#strings>`__
-  `URLs <#urls>`__
-  `Miscellaneous <#miscellaneous>`__

 ## Arrays

array\_add
~~~~~~~~~~

The ``array_add`` function adds a given key / value pair to the array if
the given key doesn't already exist in the array.

::

    $array = array('foo' => 'bar');

    $array = array_add($array, 'key', 'value');

array\_divide
~~~~~~~~~~~~~

The ``array_divide`` function returns two arrays, one containing the
keys, and the other containing the values of the original array.

::

    $array = array('foo' => 'bar');

    list($keys, $values) = array_divide($array);

array\_dot
~~~~~~~~~~

The ``array_dot`` function flattens a multi-dimensional array into a
single level array that uses "dot" notation to indicate depth.

::

    $array = array('foo' => array('bar' => 'baz'));

    $array = array_dot($array);

    // array('foo.bar' => 'baz');

array\_except
~~~~~~~~~~~~~

The ``array_except`` method removes the given key / value pairs from the
array.

::

    $array = array_except($array, array('keys', 'to', 'remove'));

array\_fetch
~~~~~~~~~~~~

The ``array_fetch`` method returns a flattened array containing the
selected nested element.

::

    $array = array(
        array('developer' => array('name' => 'Taylor')),
        array('developer' => array('name' => 'Dayle')),
    );

    $array = array_fetch($array, 'developer.name');

    // array('Taylor', 'Dayle');

array\_first
~~~~~~~~~~~~

The ``array_first`` method returns the first element of an array passing
a given truth test.

::

    $array = array(100, 200, 300);

    $value = array_first($array, function($key, $value)
    {
        return $value >= 150;
    });

A default value may also be passed as the third parameter:

::

    $value = array_first($array, $callback, $default);

array\_last
~~~~~~~~~~~

The ``array_last`` method returns the last element of an array passing a
given truth test.

::

    $array = array(350, 400, 500, 300, 200, 100);

    $value = array_last($array, function($key, $value)
    {
        return $value > 350;
    });

    // 500

A default value may also be passed as the third parameter:

::

    $value = array_last($array, $callback, $default);

array\_flatten
~~~~~~~~~~~~~~

The ``array_flatten`` method will flatten a multi-dimensional array into
a single level.

::

    $array = array('name' => 'Joe', 'languages' => array('PHP', 'Ruby'));

    $array = array_flatten($array);

    // array('Joe', 'PHP', 'Ruby');

array\_forget
~~~~~~~~~~~~~

The ``array_forget`` method will remove a given key / value pair from a
deeply nested array using "dot" notation.

::

    $array = array('names' => array('joe' => array('programmer')));

    array_forget($array, 'names.joe');

array\_get
~~~~~~~~~~

The ``array_get`` method will retrieve a given value from a deeply
nested array using "dot" notation.

::

    $array = array('names' => array('joe' => array('programmer')));

    $value = array_get($array, 'names.joe');

    $value = array_get($array, 'names.john', 'default');

    **Note:** Want something like ``array_get`` but for objects instead?
    Use ``object_get``.

array\_only
~~~~~~~~~~~

The ``array_only`` method will return only the specified key / value
pairs from the array.

::

    $array = array('name' => 'Joe', 'age' => 27, 'votes' => 1);

    $array = array_only($array, array('name', 'votes'));

array\_pluck
~~~~~~~~~~~~

The ``array_pluck`` method will pluck a list of the given key / value
pairs from the array.

::

    $array = array(array('name' => 'Taylor'), array('name' => 'Dayle'));

    $array = array_pluck($array, 'name');

    // array('Taylor', 'Dayle');

array\_pull
~~~~~~~~~~~

The ``array_pull`` method will return a given key / value pair from the
array, as well as remove it.

::

    $array = array('name' => 'Taylor', 'age' => 27);

    $name = array_pull($array, 'name');

array\_set
~~~~~~~~~~

The ``array_set`` method will set a value within a deeply nested array
using "dot" notation.

::

    $array = array('names' => array('programmer' => 'Joe'));

    array_set($array, 'names.editor', 'Taylor');

array\_sort
~~~~~~~~~~~

The ``array_sort`` method sorts the array by the results of the given
Closure.

::

    $array = array(
        array('name' => 'Jill'),
        array('name' => 'Barry'),
    );

    $array = array_values(array_sort($array, function($value)
    {
        return $value['name'];
    }));

array\_where
~~~~~~~~~~~~

Filter the array using the given Closure.

::

    $array = array(100, '200', 300, '400', 500);

    $array = array_where($array, function($key, $value)
    {
        return is_string($value);
    });

    // Array ( [1] => 200 [3] => 400 )

head
~~~~

Return the first element in the array. Useful for method chaining in PHP
5.3.x.

::

    $first = head($this->returnsArray('foo'));

last
~~~~

Return the last element in the array. Useful for method chaining.

::

    $last = last($this->returnsArray('foo'));

 ## Paths

app\_path
~~~~~~~~~

Get the fully qualified path to the ``app`` directory.

::

    $path = app_path();

base\_path
~~~~~~~~~~

Get the fully qualified path to the root of the application install.

public\_path
~~~~~~~~~~~~

Get the fully qualified path to the ``public`` directory.

storage\_path
~~~~~~~~~~~~~

Get the fully qualified path to the ``storage`` directory.

 ## Strings

camel\_case
~~~~~~~~~~~

Convert the given string to ``camelCase``.

::

    $camel = camel_case('foo_bar');

    // fooBar

class\_basename
~~~~~~~~~~~~~~~

Get the class name of the given class, without any namespace names.

::

    $class = class_basename('Foo\Bar\Baz');

    // Baz

e
~

Run ``htmlentities`` over the given string, with UTF-8 support.

::

    $entities = e('<html>foo</html>');

ends\_with
~~~~~~~~~~

Determine if the given haystack ends with a given needle.

::

    $value = ends_with('This is my name', 'name');

snake\_case
~~~~~~~~~~~

Convert the given string to ``snake_case``.

::

    $snake = snake_case('fooBar');

    // foo_bar

str\_limit
~~~~~~~~~~

Limit the number of characters in a string.

::

    str_limit($value, $limit = 100, $end = '...')

Example:

::

    $value = str_limit('The PHP framework for web artisans.', 7);

    // The PHP...

starts\_with
~~~~~~~~~~~~

Determine if the given haystack begins with the given needle.

::

    $value = starts_with('This is my name', 'This');

str\_contains
~~~~~~~~~~~~~

Determine if the given haystack contains the given needle.

::

    $value = str_contains('This is my name', 'my');

str\_finish
~~~~~~~~~~~

Add a single instance of the given needle to the haystack. Remove any
extra instances.

::

    $string = str_finish('this/string', '/');

    // this/string/

str\_is
~~~~~~~

Determine if a given string matches a given pattern. Asterisks may be
used to indicate wildcards.

::

    $value = str_is('foo*', 'foobar');

str\_plural
~~~~~~~~~~~

Convert a string to its plural form (English only).

::

    $plural = str_plural('car');

str\_random
~~~~~~~~~~~

Generate a random string of the given length.

::

    $string = str_random(40);

str\_singular
~~~~~~~~~~~~~

Convert a string to its singular form (English only).

::

    $singular = str_singular('cars');

str\_slug
~~~~~~~~~

Generate a URL friendly "slug" from a given string.

::

    str_slug($title, $separator);

Example:

::

    $title = str_slug("Laravel 5 Framework", "-");

    // laravel-5-framework

studly\_case
~~~~~~~~~~~~

Convert the given string to ``StudlyCase``.

::

    $value = studly_case('foo_bar');

    // FooBar

trans
~~~~~

Translate a given language line. Alias of ``Lang::get``.

::

    $value = trans('validation.required'):

trans\_choice
~~~~~~~~~~~~~

Translate a given language line with inflection. Alias of
``Lang::choice``.

::

    $value = trans_choice('foo.bar', $count);

 ## URLs

action
~~~~~~

Generate a URL for a given controller action.

::

    $url = action('HomeController@getIndex', $params);

route
~~~~~

Generate a URL for a given named route.

::

    $url = route('routeName', $params);

asset
~~~~~

Generate a URL for an asset.

::

    $url = asset('img/photo.jpg');

link\_to
~~~~~~~~

Generate a HTML link to the given URL.

::

    echo link_to('foo/bar', $title, $attributes = array(), $secure = null);

link\_to\_asset
~~~~~~~~~~~~~~~

Generate a HTML link to the given asset.

::

    echo link_to_asset('foo/bar.zip', $title, $attributes = array(), $secure = null);

link\_to\_route
~~~~~~~~~~~~~~~

Generate a HTML link to the given route.

::

    echo link_to_route('route.name', $title, $parameters = array(), $attributes = array());

link\_to\_action
~~~~~~~~~~~~~~~~

Generate a HTML link to the given controller action.

::

    echo link_to_action('HomeController@getIndex', $title, $parameters = array(), $attributes = array());

secure\_asset
~~~~~~~~~~~~~

Generate a HTML link to the given asset using HTTPS.

::

    echo secure_asset('foo/bar.zip', $title, $attributes = array());

secure\_url
~~~~~~~~~~~

Generate a fully qualified URL to a given path using HTTPS.

::

    echo secure_url('foo/bar', $parameters = array());

url
~~~

Generate a fully qualified URL to the given path.

::

    echo url('foo/bar', $parameters = array(), $secure = null);

 ## Miscellaneous

csrf\_token
~~~~~~~~~~~

Get the value of the current CSRF token.

::

    $token = csrf_token();

dd
~~

Dump the given variable and end execution of the script.

::

    dd($value);

value
~~~~~

If the given value is a ``Closure``, return the value returned by the
``Closure``. Otherwise, return the value.

::

    $value = value(function() { return 'bar'; });

with
~~~~

Return the given object. Useful for method chaining constructors in PHP
5.3.x.

::

    $value = with(new Foo)->doWork();

