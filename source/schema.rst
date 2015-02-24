Schema Builder
==============

-  `Introduction <#introduction>`__
-  `Creating & Dropping Tables <#creating-and-dropping-tables>`__
-  `Adding Columns <#adding-columns>`__
-  `Changing Columns <#changing-columns>`__
-  `Renaming Columns <#renaming-columns>`__
-  `Dropping Columns <#dropping-columns>`__
-  `Checking Existence <#checking-existence>`__
-  `Adding Indexes <#adding-indexes>`__
-  `Foreign Keys <#foreign-keys>`__
-  `Dropping Indexes <#dropping-indexes>`__
-  `Dropping Timestamps & Soft Deletes <#dropping-timestamps>`__
-  `Storage Engines <#storage-engines>`__

 ## Introduction

The Laravel ``Schema`` class provides a database agnostic way of
manipulating tables. It works well with all of the databases supported
by Laravel, and has a unified API across all of these systems.

 ## Creating & Dropping Tables

To create a new database table, the ``Schema::create`` method is used:

::

    Schema::create('users', function($table)
    {
        $table->increments('id');
    });

The first argument passed to the ``create`` method is the name of the
table, and the second is a ``Closure`` which will receive a
``Blueprint`` object which may be used to define the new table.

To rename an existing database table, the ``rename`` method may be used:

::

    Schema::rename($from, $to);

To specify which connection the schema operation should take place on,
use the ``Schema::connection`` method:

::

    Schema::connection('foo')->create('users', function($table)
    {
        $table->increments('id');
    });

To drop a table, you may use the ``Schema::drop`` method:

::

    Schema::drop('users');

    Schema::dropIfExists('users');

 ## Adding Columns

To update an existing table, we will use the ``Schema::table`` method:

::

    Schema::table('users', function($table)
    {
        $table->string('email');
    });

The table builder contains a variety of column types that you may use
when building your tables:

+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| Command                                             | Description                                                                          |
+=====================================================+======================================================================================+
| ``$table->bigIncrements('id');``                    | Incrementing ID using a "big integer" equivalent.                                    |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->bigInteger('votes');``                    | BIGINT equivalent to the table                                                       |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->binary('data');``                         | BLOB equivalent to the table                                                         |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->boolean('confirmed');``                   | BOOLEAN equivalent to the table                                                      |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->char('name', 4);``                        | CHAR equivalent with a length                                                        |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->date('created_at');``                     | DATE equivalent to the table                                                         |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->dateTime('created_at');``                 | DATETIME equivalent to the table                                                     |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->decimal('amount', 5, 2);``                | DECIMAL equivalent with a precision and scale                                        |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->double('column', 15, 8);``                | DOUBLE equivalent with precision, 15 digits in total and 8 after the decimal point   |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->enum('choices', array('foo', 'bar'));``   | ENUM equivalent to the table                                                         |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->float('amount');``                        | FLOAT equivalent to the table                                                        |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->increments('id');``                       | Incrementing ID to the table (primary key).                                          |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->integer('votes');``                       | INTEGER equivalent to the table                                                      |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->json('options');``                        | JSON equivalent to the table                                                         |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->longText('description');``                | LONGTEXT equivalent to the table                                                     |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->mediumInteger('numbers');``               | MEDIUMINT equivalent to the table                                                    |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->mediumText('description');``              | MEDIUMTEXT equivalent to the table                                                   |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->morphs('taggable');``                     | Adds INTEGER ``taggable_id`` and STRING ``taggable_type``                            |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->nullableTimestamps();``                   | Same as ``timestamps()``, except allows NULLs                                        |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->smallInteger('votes');``                  | SMALLINT equivalent to the table                                                     |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->tinyInteger('numbers');``                 | TINYINT equivalent to the table                                                      |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->softDeletes();``                          | Adds **deleted\_at** column for soft deletes                                         |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->string('email');``                        | VARCHAR equivalent column                                                            |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->string('name', 100);``                    | VARCHAR equivalent with a length                                                     |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->text('description');``                    | TEXT equivalent to the table                                                         |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->time('sunrise');``                        | TIME equivalent to the table                                                         |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->timestamp('added_on');``                  | TIMESTAMP equivalent to the table                                                    |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->timestamps();``                           | Adds **created\_at** and **updated\_at** columns                                     |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``$table->rememberToken();``                        | Adds ``remember_token`` as VARCHAR(100) NULL                                         |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``->nullable()``                                    | Designate that the column allows NULL values                                         |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``->default($value)``                               | Declare a default value for a column                                                 |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+
| ``->unsigned()``                                    | Set INTEGER to UNSIGNED                                                              |
+-----------------------------------------------------+--------------------------------------------------------------------------------------+

Using After On MySQL
^^^^^^^^^^^^^^^^^^^^

If you are using the MySQL database, you may use the ``after`` method to
specify the order of columns:

::

    $table->string('name')->after('email');

 ## Changing Columns

Sometimes you may need to modify an existing column. For example, you
may wish to increase the size of a string column. The ``change`` method
makes it easy! For example, let's increase the size of the ``name``
column from 25 to 50:

::

    Schema::table('users', function($table)
    {
        $table->string('name', 50)->change();
    });

We could also modify a column to be nullable:

::

    Schema::table('users', function($table)
    {
        $table->string('name', 50)->nullable()->change();
    });

 ## Renaming Columns

To rename a column, you may use the ``renameColumn`` method on the
Schema builder. Before renaming a column, be sure to add the
``doctrine/dbal`` dependency to your ``composer.json`` file.

::

    Schema::table('users', function($table)
    {
        $table->renameColumn('from', 'to');
    });

    **Note:** Renaming ``enum`` column types is not supported.

 ## Dropping Columns

To drop a column, you may use the ``dropColumn`` method on the Schema
builder. Before dropping a column, be sure to add the ``doctrine/dbal``
dependency to your ``composer.json`` file.

Dropping A Column From A Database Table
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    Schema::table('users', function($table)
    {
        $table->dropColumn('votes');
    });

Dropping Multiple Columns From A Database Table
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    Schema::table('users', function($table)
    {
        $table->dropColumn(array('votes', 'avatar', 'location'));
    });

 ## Checking Existence

Checking For Existence Of Table
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may easily check for the existence of a table or column using the
``hasTable`` and ``hasColumn`` methods:

::

    if (Schema::hasTable('users'))
    {
        //
    }

Checking For Existence Of Columns
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    if (Schema::hasColumn('users', 'email'))
    {
        //
    }

 ## Adding Indexes

The schema builder supports several types of indexes. There are two ways
to add them. First, you may fluently define them on a column definition,
or you may add them separately:

::

    $table->string('email')->unique();

Or, you may choose to add the indexes on separate lines. Below is a list
of all available index types:

+------------------------------------------------+-------------------------+
| Command                                        | Description             |
+================================================+=========================+
| ``$table->primary('id');``                     | Adding a primary key    |
+------------------------------------------------+-------------------------+
| ``$table->primary(array('first', 'last'));``   | Adding composite keys   |
+------------------------------------------------+-------------------------+
| ``$table->unique('email');``                   | Adding a unique index   |
+------------------------------------------------+-------------------------+
| ``$table->index('state');``                    | Adding a basic index    |
+------------------------------------------------+-------------------------+

 ## Foreign Keys

Laravel also provides support for adding foreign key constraints to your
tables:

::

    $table->integer('user_id')->unsigned();
    $table->foreign('user_id')->references('id')->on('users');

In this example, we are stating that the ``user_id`` column references
the ``id`` column on the ``users`` table. Make sure to create the
foreign key column first!

You may also specify options for the "on delete" and "on update" actions
of the constraint:

::

    $table->foreign('user_id')
          ->references('id')->on('users')
          ->onDelete('cascade');

To drop a foreign key, you may use the ``dropForeign`` method. A similar
naming convention is used for foreign keys as is used for other indexes:

::

    $table->dropForeign('posts_user_id_foreign');

    **Note:** When creating a foreign key that references an
    incrementing integer, remember to always make the foreign key column
    ``unsigned``.

 ## Dropping Indexes

To drop an index you must specify the index's name. Laravel assigns a
reasonable name to the indexes by default. Simply concatenate the table
name, the names of the column in the index, and the index type. Here are
some examples:

+-------------------------------------------------+--------------------------------------------------+
| Command                                         | Description                                      |
+=================================================+==================================================+
| ``$table->dropPrimary('users_id_primary');``    | Dropping a primary key from the "users" table    |
+-------------------------------------------------+--------------------------------------------------+
| ``$table->dropUnique('users_email_unique');``   | Dropping a unique index from the "users" table   |
+-------------------------------------------------+--------------------------------------------------+
| ``$table->dropIndex('geo_state_index');``       | Dropping a basic index from the "geo" table      |
+-------------------------------------------------+--------------------------------------------------+

 ## Dropping Timestamps & SoftDeletes

To drop the ``timestamps``, ``nullableTimestamps`` or ``softDeletes``
column types, you may use the following methods:

+----------------------------------+---------------------------------------------------------------------------+
| Command                          | Description                                                               |
+==================================+===========================================================================+
| ``$table->dropTimestamps();``    | Dropping the **created\_at** and **updated\_at** columns from the table   |
+----------------------------------+---------------------------------------------------------------------------+
| ``$table->dropSoftDeletes();``   | Dropping **deleted\_at** column from the table                            |
+----------------------------------+---------------------------------------------------------------------------+

 ## Storage Engines

To set the storage engine for a table, set the ``engine`` property on
the schema builder:

::

    Schema::create('users', function($table)
    {
        $table->engine = 'InnoDB';

        $table->string('email');
    });

