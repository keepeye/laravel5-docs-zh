Localization
============

-  `Introduction <#introduction>`__
-  `Language Files <#language-files>`__
-  `Basic Usage <#basic-usage>`__
-  `Pluralization <#pluralization>`__
-  `Validation Localization <#validation>`__
-  `Overriding Package Language
   Files <#overriding-package-language-files>`__

 ## Introduction

The Laravel ``Lang`` facade provides a convenient way of retrieving
strings in various languages, allowing you to easily support multiple
languages within your application.

 ## Language Files

Language strings are stored in files within the ``resources/lang``
directory. Within this directory there should be a subdirectory for each
language supported by the application.

::

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

Example Language File
^^^^^^^^^^^^^^^^^^^^^

Language files simply return an array of keyed strings. For example:

::

    <?php

    return array(
        'welcome' => 'Welcome to our application'
    );

Changing The Default Language At Runtime
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The default language for your application is stored in the
``config/app.php`` configuration file. You may change the active
language at any time using the ``App::setLocale`` method:

::

    App::setLocale('es');

Setting The Fallback Language
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may also configure a "fallback language", which will be used when
the active language does not contain a given language line. Like the
default language, the fallback language is also configured in the
``config/app.php`` configuration file:

::

    'fallback_locale' => 'en',

 ## Basic Usage

Retrieving Lines From A Language File
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    echo Lang::get('messages.welcome');

The first segment of the string passed to the ``get`` method is the name
of the language file, and the second is the name of the line that should
be retrieved.

    **Note:** If a language line does not exist, the key will be
    returned by the ``get`` method.

You may also use the ``trans`` helper function, which is an alias for
the ``Lang::get`` method.

::

    echo trans('messages.welcome');

Making Replacements In Lines
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may also define place-holders in your language lines:

::

    'welcome' => 'Welcome, :name',

Then, pass a second argument of replacements to the ``Lang::get``
method:

::

    echo Lang::get('messages.welcome', array('name' => 'Dayle'));

Determine If A Language File Contains A Line
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    if (Lang::has('messages.welcome'))
    {
        //
    }

 ## Pluralization

Pluralization is a complex problem, as different languages have a
variety of complex rules for pluralization. You may easily manage this
in your language files. By using a "pipe" character, you may separate
the singular and plural forms of a string:

::

    'apples' => 'There is one apple|There are many apples',

You may then use the ``Lang::choice`` method to retrieve the line:

::

    echo Lang::choice('messages.apples', 10);

You may also supply a locale argument to specify the language. For
example, if you want to use the Russian (ru) language:

::

    echo Lang::choice('товар|товара|товаров', $count, array(), 'ru');

Since the Laravel translator is powered by the Symfony Translation
component, you may also create more explicit pluralization rules easily:

::

    'apples' => '{0} There are none|[1,19] There are some|[20,Inf] There are many',

 ## Validation

For localization for validation errors and messages, take a look at the
documentation on Validation.

 ## Overriding Package Language Files

Many packages ship with their own language lines. Instead of hacking the
package's core files to tweak these lines, you may override them by
placing files in the ``resources/lang/packages/{locale}/{package}``
directory. So, for example, if you need to override the English language
lines in ``messages.php`` for a package named ``skyrim/hearthfire``, you
would place a language file at:
``resources/lang/packages/en/hearthfire/messages.php``. In this file you
would define only the language lines you wish to override. Any language
lines you don't override will still be loaded from the package's
language files.
