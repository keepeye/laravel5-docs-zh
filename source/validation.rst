Validation
==========

-  `Basic Usage <#basic-usage>`__
-  `Controller Validation <#controller-validation>`__
-  `Form Request Validation <#form-request-validation>`__
-  `Working With Error Messages <#working-with-error-messages>`__
-  `Error Messages & Views <#error-messages-and-views>`__
-  `Available Validation Rules <#available-validation-rules>`__
-  `Conditionally Adding Rules <#conditionally-adding-rules>`__
-  `Custom Error Messages <#custom-error-messages>`__
-  `Custom Validation Rules <#custom-validation-rules>`__

 ## Basic Usage

Laravel ships with a simple, convenient facility for validating data and
retrieving validation error messages via the ``Validation`` class.

Basic Validation Example
^^^^^^^^^^^^^^^^^^^^^^^^

::

    $validator = Validator::make(
        array('name' => 'Dayle'),
        array('name' => 'required|min:5')
    );

The first argument passed to the ``make`` method is the data under
validation. The second argument is the validation rules that should be
applied to the data.

Using Arrays To Specify Rules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Multiple rules may be delimited using either a "pipe" character, or as
separate elements of an array.

::

    $validator = Validator::make(
        array('name' => 'Dayle'),
        array('name' => array('required', 'min:5'))
    );

Validating Multiple Fields
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    $validator = Validator::make(
        array(
            'name' => 'Dayle',
            'password' => 'lamepassword',
            'email' => 'email@example.com'
        ),
        array(
            'name' => 'required',
            'password' => 'required|min:8',
            'email' => 'required|email|unique:users'
        )
    );

Once a ``Validator`` instance has been created, the ``fails`` (or
``passes``) method may be used to perform the validation.

::

    if ($validator->fails())
    {
        // The given data did not pass validation
    }

If validation has failed, you may retrieve the error messages from the
validator.

::

    $messages = $validator->messages();

You may also access an array of the failed validation rules, without
messages. To do so, use the ``failed`` method:

::

    $failed = $validator->failed();

Validating Files
^^^^^^^^^^^^^^^^

The ``Validator`` class provides several rules for validating files,
such as ``size``, ``mimes``, and others. When validating files, you may
simply pass them into the validator with your other data.

After Validation Hook
~~~~~~~~~~~~~~~~~~~~~

The validator also allows you to attach callbacks to be run after
validation is completed. This allows you to easily perform further
validation, and even add more error messages to the message collection.
To get started, use the ``after`` method on a validator instance:

::

    $validator = Validator::make(...);

    $validator->after(function($validator)
    {
        if ($this->somethingElseIsInvalid())
        {
            $validator->errors()->add('field', 'Something is wrong with this field!');
        }
    });

    if ($validator->fails())
    {
        //
    }

You may add as many ``after`` callbacks to a validator as needed.

 ## Controller Validation

Of course, manually creating and checking a ``Validator`` instance each
time you do validation is a headache. Don't worry, you have other
options! The base ``App\Http\Controllers\Controller`` class included
with Laravel uses a ``ValidatesRequests`` trait. This trait provides a
single, convenient method for validating incoming HTTP requests. Here's
what it looks like:

::

    /**
     * Store the incoming blog post.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $this->validate($request, [
            'title' => 'required|unique|max:255',
            'body' => 'required',
        ]);

        //
    }

If validation passes, your code will keep executing normally. However,
if validation fails, an
``Illuminate\Contracts\Validation\ValidationException`` will be thrown.
This exception is automatically caught and a redirect is generated to
the user's previous location. The validation errors are even
automatically flashed to the session!

If the incoming request was an AJAX request, no redirect will be
generated. Instead, an HTTP response with a 422 status code will be
returned to the browser containing a JSON representation of the
validation errors.

For example, here is the equivalent code written manually:

::

    /**
     * Store the incoming blog post.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $v = Validator::make($request->all(), [
            'title' => 'required|unique|max:255',
            'body' => 'required',
        ]);

        if ($v->fails())
        {
            return redirect()->back()->withErrors($v->errors());
        }

        //
    }

Customizing The Flashed Error Format
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you wish to customize the format of the validation errors that are
flashed to the session when validation fails, override the
``formatValidationErrors`` on your base controller. Don't forget to
import the ``Illuminate\Validation\Validator`` class at the top of the
file:

::

    /**
     * {@inheritdoc}
     */
    protected function formatValidationErrors(Validator $validator)
    {
        return $validator->errors()->all();
    }

 ## Form Request Validation

For more complex validation scenarios, you may wish to create a "form
request". Form requests are custom request classes that contain
validation logic. To create a form request class, use the
``make:request`` Artisan CLI command:

::

    php artisan make:request StoreBlogPostRequest

The generated class will be placed in the ``app/Http/Requests``
directory. Let's add a few validation rules to the ``rules`` method:

::

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'title' => 'required|unique|max:255',
            'body' => 'required',
        ];
    }

So, how are the validation rules executed? All you need to do is
type-hint the request on your controller method:

::

    /**
     * Store the incoming blog post.
     *
     * @param  StoreBlogPostRequest  $request
     * @return Response
     */
    public function store(StoreBlogPostRequest $request)
    {
        // The incoming request is valid...
    }

The incoming form request is validated before the controller method is
called, meaning you do not need to clutter your controller with any
validation logic. It has already been validated!

If validation fails, a redirect response will be generated to send the
user back to their previous location. The errors will also be flashed to
the session so they are available for display. If the request was an
AJAX request, a HTTP response with a 422 status code will be returned to
the user including a JSON representation of the validation errors.

Authorizing Form Requests
~~~~~~~~~~~~~~~~~~~~~~~~~

The form request class also contains an ``authorize`` method. Within
this method, you may check if the authenticated user actually has the
authority to update a given resource. For example, if a user is
attempting to update a blog post comment, do they actually own that
comment? For example:

::

    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        $commentId = $this->route('comment');

        return Comment::where('id', $commentId)
                      ->where('user_id', Auth::id())->exists();
    }

Note the call to the ``route`` method in the example above. This method
grants you access to the URI parameters defined on the route being
called, such as the ``{comment}`` parameter in the example below:

::

    Route::post('comment/{comment}');

If the ``authorize`` method returns ``false``, a HTTP response with a
403 status code will automatically be returned and your controller
method will not execute.

If you plan to have authorization logic in another part of your
application, simply return ``true`` from the ``authorize`` method:

::

    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

Customizing The Flashed Error Format
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you wish to customize the format of the validation errors that are
flashed to the session when validation fails, override the
``formatValidationErrors`` on your base request
(``App\Http\Requests\Request``). Don't forget to import the
``Illuminate\Validation\Validator`` class at the top of the file:

::

    /**
     * {@inheritdoc}
     */
    protected function formatErrors(Validator $validator)
    {
        return $validator->errors()->all();
    }

 ## Working With Error Messages

After calling the ``messages`` method on a ``Validator`` instance, you
will receive a ``MessageBag`` instance, which has a variety of
convenient methods for working with error messages.

Retrieving The First Error Message For A Field
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    echo $messages->first('email');

Retrieving All Error Messages For A Field
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    foreach ($messages->get('email') as $message)
    {
        //
    }

Retrieving All Error Messages For All Fields
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    foreach ($messages->all() as $message)
    {
        //
    }

Determining If Messages Exist For A Field
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    if ($messages->has('email'))
    {
        //
    }

Retrieving An Error Message With A Format
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    echo $messages->first('email', '<p>:message</p>');

    **Note:** By default, messages are formatted using Bootstrap
    compatible syntax.

Retrieving All Error Messages With A Format
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    foreach ($messages->all('<li>:message</li>') as $message)
    {
        //
    }

 ## Error Messages & Views

Once you have performed validation, you will need an easy way to get the
error messages back to your views. This is conveniently handled by
Laravel. Consider the following routes as an example:

::

    Route::get('register', function()
    {
        return View::make('user.register');
    });

    Route::post('register', function()
    {
        $rules = array(...);

        $validator = Validator::make(Input::all(), $rules);

        if ($validator->fails())
        {
            return redirect('register')->withErrors($validator);
        }
    });

Note that when validation fails, we pass the ``Validator`` instance to
the Redirect using the ``withErrors`` method. This method will flash the
error messages to the session so that they are available on the next
request.

However, notice that we do not have to explicitly bind the error
messages to the view in our GET route. This is because Laravel will
always check for errors in the session data, and automatically bind them
to the view if they are available. **So, it is important to note that an
``$errors`` variable will always be available in all of your views, on
every request**, allowing you to conveniently assume the ``$errors``
variable is always defined and can be safely used. The ``$errors``
variable will be an instance of ``MessageBag``.

So, after redirection, you may utilize the automatically bound
``$errors`` variable in your view:

::

    <?php echo $errors->first('email'); ?>

Named Error Bags
~~~~~~~~~~~~~~~~

If you have multiple forms on a single page, you may wish to name the
``MessageBag`` of errors. This will allow you to retrieve the error
messages for a specific form. Simply pass a name as the second argument
to ``withErrors``:

::

    return redirect('register')->withErrors($validator, 'login');

You may then access the named ``MessageBag`` instance from the
``$errors`` variable:

::

    <?php echo $errors->login->first('email'); ?>

 ## Available Validation Rules

Below is a list of all available validation rules and their function:

-  `Accepted <#rule-accepted>`__
-  `Active URL <#rule-active-url>`__
-  `After (Date) <#rule-after>`__
-  `Alpha <#rule-alpha>`__
-  `Alpha Dash <#rule-alpha-dash>`__
-  `Alpha Numeric <#rule-alpha-num>`__
-  `Array <#rule-array>`__
-  `Before (Date) <#rule-before>`__
-  `Between <#rule-between>`__
-  `Boolean <#rule-boolean>`__
-  `Confirmed <#rule-confirmed>`__
-  `Date <#rule-date>`__
-  `Date Format <#rule-date-format>`__
-  `Different <#rule-different>`__
-  `Digits <#rule-digits>`__
-  `Digits Between <#rule-digits-between>`__
-  `E-Mail <#rule-email>`__
-  `Exists (Database) <#rule-exists>`__
-  `Image (File) <#rule-image>`__
-  `In <#rule-in>`__
-  `Integer <#rule-integer>`__
-  `IP Address <#rule-ip>`__
-  `Max <#rule-max>`__
-  `MIME Types <#rule-mimes>`__
-  `Min <#rule-min>`__
-  `Not In <#rule-not-in>`__
-  `Numeric <#rule-numeric>`__
-  `Regular Expression <#rule-regex>`__
-  `Required <#rule-required>`__
-  `Required If <#rule-required-if>`__
-  `Required With <#rule-required-with>`__
-  `Required With All <#rule-required-with-all>`__
-  `Required Without <#rule-required-without>`__
-  `Required Without All <#rule-required-without-all>`__
-  `Same <#rule-same>`__
-  `Size <#rule-size>`__
-  `String <#rule-string>`__
-  `Timezone <#rule-timezone>`__
-  `Unique (Database) <#rule-unique>`__
-  `URL <#rule-url>`__

 #### accepted

The field under validation must be *yes*, *on*, or *1*. This is useful
for validating "Terms of Service" acceptance.

 #### active\_url

The field under validation must be a valid URL according to the
``checkdnsrr`` PHP function.

 #### after:\ *date*

The field under validation must be a value after a given date. The dates
will be passed into the PHP ``strtotime`` function.

 #### alpha

The field under validation must be entirely alphabetic characters.

 #### alpha\_dash

The field under validation may have alpha-numeric characters, as well as
dashes and underscores.

 #### alpha\_num

The field under validation must be entirely alpha-numeric characters.

 #### array

The field under validation must be of type array.

 #### before:\ *date*

The field under validation must be a value preceding the given date. The
dates will be passed into the PHP ``strtotime`` function.

 #### between:\ *min*,\ *max*

The field under validation must have a size between the given *min* and
*max*. Strings, numerics, and files are evaluated in the same fashion as
the ``size`` rule.

 #### boolean

The field under validation must be able to be cast as a boolean.
Accepted input are ``true``, ``false``, ``1``, ``0``, ``"1"`` and
``"0"``.

 #### confirmed

The field under validation must have a matching field of
``foo_confirmation``. For example, if the field under validation is
``password``, a matching ``password_confirmation`` field must be present
in the input.

 #### date

The field under validation must be a valid date according to the
``strtotime`` PHP function.

 #### date\_format:\ *format*

The field under validation must match the *format* defined according to
the ``date_parse_from_format`` PHP function.

 #### different:\ *field*

The given *field* must be different than the field under validation.

 #### digits:\ *value*

The field under validation must be *numeric* and must have an exact
length of *value*.

 #### digits\_between:\ *min*,\ *max*

The field under validation must have a length between the given *min*
and *max*.

 #### email

The field under validation must be formatted as an e-mail address.

 #### exists:\ *table*,\ *column*

The field under validation must exist on a given database table.

Basic Usage Of Exists Rule
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    'state' => 'exists:states'

Specifying A Custom Column Name
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    'state' => 'exists:states,abbreviation'

You may also specify more conditions that will be added as "where"
clauses to the query:

::

    'email' => 'exists:staff,email,account_id,1'

Passing ``NULL`` as a "where" clause value will add a check for a
``NULL`` database value:

::

    'email' => 'exists:staff,email,deleted_at,NULL'

 #### image

The file under validation must be an image (jpeg, png, bmp, gif, or svg)

 #### in:\ *foo*,\ *bar*,...

The field under validation must be included in the given list of values.

 #### integer

The field under validation must have an integer value.

 #### ip

The field under validation must be formatted as an IP address.

 #### max:\ *value*

The field under validation must be less than or equal to a maximum
*value*. Strings, numerics, and files are evaluated in the same fashion
as the ```size`` <#rule-size>`__ rule.

 #### mimes:\ *foo*,\ *bar*,...

The file under validation must have a MIME type corresponding to one of
the listed extensions.

Basic Usage Of MIME Rule
^^^^^^^^^^^^^^^^^^^^^^^^

::

    'photo' => 'mimes:jpeg,bmp,png'

 #### min:\ *value*

The field under validation must have a minimum *value*. Strings,
numerics, and files are evaluated in the same fashion as the
```size`` <#rule-size>`__ rule.

 #### not\_in:\ *foo*,\ *bar*,...

The field under validation must not be included in the given list of
values.

 #### numeric

The field under validation must have a numeric value.

 #### regex:\ *pattern*

The field under validation must match the given regular expression.

**Note:** When using the ``regex`` pattern, it may be necessary to
specify rules in an array instead of using pipe delimiters, especially
if the regular expression contains a pipe character.

 #### required

The field under validation must be present in the input data.

 #### required\_if:\ *field*,\ *value*,...

The field under validation must be present if the *field* field is equal
to any *value*.

 #### required\_with:\ *foo*,\ *bar*,...

The field under validation must be present *only if* any of the other
specified fields are present.

 #### required\_with\_all:\ *foo*,\ *bar*,...

The field under validation must be present *only if* all of the other
specified fields are present.

 #### required\_without:\ *foo*,\ *bar*,...

The field under validation must be present *only when* any of the other
specified fields are not present.

 #### required\_without\_all:\ *foo*,\ *bar*,...

The field under validation must be present *only when* all of the other
specified fields are not present.

 #### same:\ *field*

The given *field* must match the field under validation.

 #### size:\ *value*

The field under validation must have a size matching the given *value*.
For string data, *value* corresponds to the number of characters. For
numeric data, *value* corresponds to a given integer value. For files,
*size* corresponds to the file size in kilobytes.

 #### string:\ *value*

The field under validation must be a string type.

 #### timezone

The field under validation must be a valid timezone identifier according
to the ``timezone_identifiers_list`` PHP function.

 #### unique:\ *table*,\ *column*,\ *except*,\ *idColumn*

The field under validation must be unique on a given database table. If
the ``column`` option is not specified, the field name will be used.

Basic Usage Of Unique Rule
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    'email' => 'unique:users'

Specifying A Custom Column Name
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    'email' => 'unique:users,email_address'

Forcing A Unique Rule To Ignore A Given ID
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    'email' => 'unique:users,email_address,10'

Adding Additional Where Clauses
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may also specify more conditions that will be added as "where"
clauses to the query:

::

    'email' => 'unique:users,email_address,NULL,id,account_id,1'

In the rule above, only rows with an ``account_id`` of ``1`` would be
included in the unique check.

 #### url

The field under validation must be formatted as an URL.

    **Note:** This function uses PHP's ``filter_var`` method.

 ## Conditionally Adding Rules

In some situations, you may wish to run validation checks against a
field **only** if that field is present in the input array. To quickly
accomplish this, add the ``sometimes`` rule to your rule list:

::

    $v = Validator::make($data, array(
        'email' => 'sometimes|required|email',
    ));

In the example above, the ``email`` field will only be validated if it
is present in the ``$data`` array.

Complex Conditional Validation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sometimes you may wish to require a given field only if another field
has a greater value than 100. Or you may need two fields to have a given
value only when another field is present. Adding these validation rules
doesn't have to be a pain. First, create a ``Validator`` instance with
your *static rules* that never change:

::

    $v = Validator::make($data, array(
        'email' => 'required|email',
        'games' => 'required|numeric',
    ));

Let's assume our web application is for game collectors. If a game
collector registers with our application and they own more than 100
games, we want them to explain why they own so many games. For example,
perhaps they run a game re-sell shop, or maybe they just enjoy
collecting. To conditionally add this requirement, we can use the
``sometimes`` method on the ``Validator`` instance.

::

    $v->sometimes('reason', 'required|max:500', function($input)
    {
        return $input->games >= 100;
    });

The first argument passed to the ``sometimes`` method is the name of the
field we are conditionally validating. The second argument is the rules
we want to add. If the ``Closure`` passed as the third argument returns
``true``, the rules will be added. This method makes it a breeze to
build complex conditional validations. You may even add conditional
validations for several fields at once:

::

    $v->sometimes(array('reason', 'cost'), 'required', function($input)
    {
        return $input->games >= 100;
    });

    **Note:** The ``$input`` parameter passed to your ``Closure`` will
    be an instance of ``Illuminate\Support\Fluent`` and may be used as
    an object to access your input and files.

 ## Custom Error Messages

If needed, you may use custom error messages for validation instead of
the defaults. There are several ways to specify custom messages.

Passing Custom Messages Into Validator
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    $messages = array(
        'required' => 'The :attribute field is required.',
    );

    $validator = Validator::make($input, $rules, $messages);

    *Note:* The ``:attribute`` place-holder will be replaced by the
    actual name of the field under validation. You may also utilize
    other place-holders in validation messages.

Other Validation Place-Holders
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    $messages = array(
        'same'    => 'The :attribute and :other must match.',
        'size'    => 'The :attribute must be exactly :size.',
        'between' => 'The :attribute must be between :min - :max.',
        'in'      => 'The :attribute must be one of the following types: :values',
    );

Specifying A Custom Message For A Given Attribute
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sometimes you may wish to specify a custom error messages only for a
specific field:

::

    $messages = array(
        'email.required' => 'We need to know your e-mail address!',
    );

 #### Specifying Custom Messages In Language Files

In some cases, you may wish to specify your custom messages in a
language file instead of passing them directly to the ``Validator``. To
do so, add your messages to ``custom`` array in the
``resources/lang/xx/validation.php`` language file.

::

    'custom' => array(
        'email' => array(
            'required' => 'We need to know your e-mail address!',
        ),
    ),

 ## Custom Validation Rules

Registering A Custom Validation Rule
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Laravel provides a variety of helpful validation rules; however, you may
wish to specify some of your own. One method of registering custom
validation rules is using the ``Validator::extend`` method:

::

    Validator::extend('foo', function($attribute, $value, $parameters)
    {
        return $value == 'foo';
    });

The custom validator Closure receives three arguments: the name of the
``$attribute`` being validated, the ``$value`` of the attribute, and an
array of ``$parameters`` passed to the rule.

You may also pass a class and method to the ``extend`` method instead of
a Closure:

::

    Validator::extend('foo', 'FooValidator@validate');

Note that you will also need to define an error message for your custom
rules. You can do so either using an inline custom message array or by
adding an entry in the validation language file.

Extending The Validator Class
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Instead of using Closure callbacks to extend the Validator, you may also
extend the Validator class itself. To do so, write a Validator class
that extends ``Illuminate\Validation\Validator``. You may add validation
methods to the class by prefixing them with ``validate``:

::

    <?php

    class CustomValidator extends Illuminate\Validation\Validator {

        public function validateFoo($attribute, $value, $parameters)
        {
            return $value == 'foo';
        }

    }

Registering A Custom Validator Resolver
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next, you need to register your custom Validator extension:

::

    Validator::resolver(function($translator, $data, $rules, $messages)
    {
        return new CustomValidator($translator, $data, $rules, $messages);
    });

When creating a custom validation rule, you may sometimes need to define
custom place-holder replacements for error messages. You may do so by
creating a custom Validator as described above, and adding a
``replaceXXX`` function to the validator.

::

    protected function replaceFoo($message, $attribute, $rule, $parameters)
    {
        return str_replace(':foo', $parameters[0], $message);
    }

If you would like to add a custom message "replacer" without extending
the ``Validator`` class, you may use the ``Validator::replacer`` method:

::

    Validator::replacer('rule', function($message, $attribute, $rule, $parameters)
    {
        //
    });

