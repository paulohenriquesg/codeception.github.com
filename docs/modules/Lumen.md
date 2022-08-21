---
layout: doc
title: Lumen - Codeception - Documentation
---



<div class="btn-group" role="group" style="float: right" aria-label="..."><a class="btn btn-default" href="https://github.com/Codeception/module-Lumen/releases">Releases</a><a class="btn btn-default" href="https://github.com/Codeception/module-lumen/tree/master/src/Codeception/Module/Lumen.php"><strong>source</strong></a></div>

# Lumen
### Installation

If you use Codeception installed using composer, install this module with the following command:

{% highlight yaml %}
composer require --dev codeception/module-lumen

{% endhighlight %}

Alternatively, you can enable `Lumen` module in suite configuration file and run
 
{% highlight yaml %}
codecept init upgrade4

{% endhighlight %}

This module was bundled with Codeception 2 and 3, but since version 4 it is necessary to install it separately.   
Some modules are bundled with PHAR files.  
Warning. Using PHAR file and composer in the same project can cause unexpected errors.  

### Description




This module allows you to run functional tests for Lumen.
Please try it and leave your feedback.

### Demo project
<https://github.com/codeception/lumen-module-tests>


### Config

* cleanup: `boolean`, default `true` - all database queries will be run in a transaction,
  which will be rolled back at the end of each test.
* bootstrap: `string`, default `bootstrap/app.php` - relative path to app.php config file.
* root: `string`, default `` - root path of the application.
* packages: `string`, default `workbench` - root path of application packages (if any).
* url: `string`, default `http://localhost` - the application URL

### API

* app - `\Laravel\Lumen\Application`
* config - `array`

### Parts

* `ORM`: Only include the database methods of this module:
    * dontSeeRecord
    * grabRecord
    * have
    * haveMultiple
    * haveRecord
    * make
    * makeMultiple
    * seeRecord

See [WebDriver module](https://codeception.com/docs/modules/WebDriver#Loading-Parts-from-other-Modules)
for general information on how to load parts of a framework module.

### Actions

#### _findElements

*hidden API method, expected to be used from Helper classes*

* `api` 
* `param mixed` $locator
* `return iterable`

Locates element using available Codeception locator types:

* XPath
* CSS
* Strict Locator

Use it in Helpers or GroupObject or Extension classes:

{% highlight php %}

<?php
$els = $this->getModule('Lumen')->_findElements('.items');
$els = $this->getModule('Lumen')->_findElements(['name' => 'username']);

$editLinks = $this->getModule('Lumen')->_findElements(['link' => 'Edit']);
// now you can iterate over $editLinks and check that all them have valid hrefs

{% endhighlight %}

WebDriver module returns `Facebook\WebDriver\Remote\RemoteWebElement` instances
PhpBrowser and Framework modules return `Symfony\Component\DomCrawler\Crawler` instances


#### _getResponseContent

*hidden API method, expected to be used from Helper classes*

* `api` 
* `throws ModuleException`
* `return string`

Returns content of the last response
Use it in Helpers when you want to retrieve response of request performed by another module.

{% highlight php %}

<?php
// in Helper class
public function seeResponseContains($text)
{
   $this->assertStringContainsString($text, $this->getModule('Lumen')->_getResponseContent(), "response contains");
}

{% endhighlight %}


#### _loadPage

*hidden API method, expected to be used from Helper classes*

* `api` 
* `param string` $method
* `param string` $uri
* `param array` $parameters
* `param array` $files
* `param array` $server
* `param ?string` $content
* `return void`

Opens a page with arbitrary request parameters.

Useful for testing multi-step forms on a specific step.

{% highlight php %}

<?php
// in Helper class
public function openCheckoutFormStep2($orderId) {
    $this->getModule('Lumen')->_loadPage('POST', '/checkout/step2', ['order' => $orderId]);
}

{% endhighlight %}


#### _request

*hidden API method, expected to be used from Helper classes*

* `api` 
* `see` `_loadPage`
* `param string` $method
* `param string` $uri
* `param array` $parameters
* `param array` $files
* `param array` $server
* `param ?string` $content
* `throws ExternalUrlException|ModuleException`
* `return ?string`

Send custom request to a backend using method, uri, parameters, etc.

Use it in Helpers to create special request actions, like accessing API
Returns a string with response body.

{% highlight php %}

<?php
// in Helper class
public function createUserByApi($name) {
    $userData = $this->getModule('Lumen')->_request('POST', '/api/v1/users', ['name' => $name]);
    $user = json_decode($userData);
    return $user->id;
}

{% endhighlight %}
Does not load the response into the module so you can't interact with response page (click, fill forms).
To load arbitrary page for interaction, use `_loadPage` method.


#### _savePageSource

*hidden API method, expected to be used from Helper classes*

* `api` 
* `param string` $filename
* `return void`

Saves page source of to a file

{% highlight php %}

$this->getModule('Lumen')->_savePageSource(codecept_output_dir().'page.html');

{% endhighlight %}


#### amHttpAuthenticated

* `param string` $username
* `param string` $password
* `return void`

Authenticates user for HTTP_AUTH


#### amLoggedAs

* `param \Illuminate\Contracts\Auth\Authenticatable` $user
* `param ?string` $guardName
* `return void`

Set the authenticated user for the next request.

This will not persist between multiple requests.


#### amOnPage

* `param string` $page
* `return void`

Opens the page for the given relative URI.

{% highlight php %}

<?php
// opens front page
$I->amOnPage('/');
// opens /register page
$I->amOnPage('/register');

{% endhighlight %}


#### amOnRoute

* `param string` $routeName
* `param array` $params
* `return void`

Opens web page using route name and parameters.

{% highlight php %}

<?php
$I->amOnRoute('homepage');

{% endhighlight %}


#### attachFile

* `param ` $field
* `param string` $filename
* `return void`

Attaches a file relative to the Codeception `_data` directory to the given file upload field.

{% highlight php %}

<?php
// file is stored in 'tests/_data/prices.xls'
$I->attachFile('input[@type="file"]', 'prices.xls');

{% endhighlight %}


#### checkOption

* `param ` $option
* `return void`

Ticks a checkbox. For radio buttons, use the `selectOption` method instead.

{% highlight php %}

<?php
$I->checkOption('#agree');

{% endhighlight %}


#### clearApplicationHandlers

* `return void`

Clear the registered application handlers.

{% highlight php %}

<?php
$I->clearApplicationHandlers();

{% endhighlight %}


#### click

* `param string|array` $link
* `param ` $context
* `return void`

Perform a click on a link or a button, given by a locator.

If a fuzzy locator is given, the page will be searched for a button, link, or image matching the locator string.
For buttons, the "value" attribute, "name" attribute, and inner text are searched.
For links, the link text is searched.
For images, the "alt" attribute and inner text of any parent links are searched.

The second parameter is a context (CSS or XPath locator) to narrow the search.

Note that if the locator matches a button of type `submit`, the form will be submitted.

{% highlight php %}

<?php
// simple link
$I->click('Logout');
// button of form
$I->click('Submit');
// CSS button
$I->click('#form input[type=submit]');
// XPath
$I->click('//form/*[@type="submit"]');
// link in context
$I->click('Logout', '#nav');
// using strict locator
$I->click(['link' => 'Login']);

{% endhighlight %}


#### deleteHeader

* `param string` $name the name of the header to delete.
* `return void`

Deletes the header with the passed name.  Subsequent requests
will not have the deleted header in its request.

Example:
{% highlight php %}

<?php
$I->haveHttpHeader('X-Requested-With', 'Codeception');
$I->amOnPage('test-headers.php');
// ...
$I->deleteHeader('X-Requested-With');
$I->amOnPage('some-other-page.php');

{% endhighlight %}


#### dontSee

* `param array|string` $selector optional
* `param string` $text
* `return void`

Checks that the current page doesn't contain the text specified (case insensitive).

Give a locator as the second parameter to match a specific region.

{% highlight php %}

<?php
$I->dontSee('Login');                         // I can suppose user is already logged in
$I->dontSee('Sign Up','h1');                  // I can suppose it's not a signup page
$I->dontSee('Sign Up','//body/h1');           // with XPath
$I->dontSee('Sign Up', ['css' => 'body h1']); // with strict CSS locator

{% endhighlight %}

Note that the search is done after stripping all HTML tags from the body,
so `$I->dontSee('strong')` will fail on strings like:

  - `<p>I am Stronger than thou</p>`
  - `<script>document.createElement('strong');</script>`

But will ignore strings like:

  - `<strong>Home</strong>`
  - `<div class="strong">Home</strong>`
  - `<!-- strong -->`

For checking the raw source code, use `seeInSource()`.


#### dontSeeAuthentication

* `return void`

Check that user is not authenticated.


#### dontSeeCheckboxIsChecked

* `param ` $checkbox
* `return void`

Check that the specified checkbox is unchecked.

{% highlight php %}

<?php
$I->dontSeeCheckboxIsChecked('#agree'); // I suppose user didn't agree to terms
$I->seeCheckboxIsChecked('#signup_form input[type=checkbox]'); // I suppose user didn't check the first checkbox in form.

{% endhighlight %}


#### dontSeeCookie

* `param ` $cookie
* `param ` $params
* `return mixed|void`

Checks that there isn't a cookie with the given name.

You can set additional cookie params like `domain`, `path` as array passed in last argument.


#### dontSeeCurrentUrlEquals

* `param string` $uri
* `return void`

Checks that the current URL doesn't equal the given string.

Unlike `dontSeeInCurrentUrl`, this only matches the full URL.

{% highlight php %}

<?php
// current url is not root
$I->dontSeeCurrentUrlEquals('/');

{% endhighlight %}


#### dontSeeCurrentUrlMatches

* `param string` $uri
* `return void`

Checks that current url doesn't match the given regular expression.

{% highlight php %}

<?php
// to match root url
$I->dontSeeCurrentUrlMatches('~^/users/(\d+)~');

{% endhighlight %}


#### dontSeeElement

* `param ` $selector
* `param array` $attributes
* `return void`

Checks that the given element is invisible or not present on the page.

You can also specify expected attributes of this element.

{% highlight php %}

<?php
$I->dontSeeElement('.error');
$I->dontSeeElement('//form/input[1]');
$I->dontSeeElement('input', ['name' => 'login']);
$I->dontSeeElement('input', ['value' => '123456']);

{% endhighlight %}


#### dontSeeInCurrentUrl

* `param string` $uri
* `return void`

Checks that the current URI doesn't contain the given string.

{% highlight php %}

<?php
$I->dontSeeInCurrentUrl('/users/');

{% endhighlight %}


#### dontSeeInField

* `param string|array` $field
* `param ` $value
* `return void`

Checks that an input field or textarea doesn't contain the given value.

For fuzzy locators, the field is matched by label text, CSS and XPath.

{% highlight php %}

<?php
$I->dontSeeInField('Body','Type your comment here');
$I->dontSeeInField('form textarea[name=body]','Type your comment here');
$I->dontSeeInField('form input[type=hidden]','hidden_value');
$I->dontSeeInField('#searchform input','Search');
$I->dontSeeInField('//form/*[@name=search]','Search');
$I->dontSeeInField(['name' => 'search'], 'Search');

{% endhighlight %}


#### dontSeeInFormFields

* `param ` $formSelector
* `param array` $params
* `return void`

Checks if the array of form parameters (name => value) are not set on the form matched with
the passed selector.

{% highlight php %}

<?php
$I->dontSeeInFormFields('form[name=myform]', [
     'input1' => 'non-existent value',
     'input2' => 'other non-existent value',
]);

{% endhighlight %}

To check that an element hasn't been assigned any one of many values, an array can be passed
as the value:

{% highlight php %}

<?php
$I->dontSeeInFormFields('.form-class', [
     'fieldName' => [
         'This value shouldn\'t be set',
         'And this value shouldn\'t be set',
     ],
]);

{% endhighlight %}

Additionally, checkbox values can be checked with a boolean.

{% highlight php %}

<?php
$I->dontSeeInFormFields('#form-id', [
     'checkbox1' => true,        // fails if checked
     'checkbox2' => false,       // fails if unchecked
]);

{% endhighlight %}


#### dontSeeInSource

* `param string` $raw
* `return void`

Checks that the current page contains the given string in its
raw source code.

{% highlight php %}

<?php
$I->dontSeeInSource('<h1>Green eggs &amp; ham</h1>');

{% endhighlight %}


#### dontSeeInTitle

* `param ` $title
* `return mixed|void`

Checks that the page title does not contain the given string.


#### dontSeeLink

* `param string` $text
* `param string` $url
* `return void`

Checks that the page doesn't contain a link with the given string.

If the second parameter is given, only links with a matching "href" attribute will be checked.

{% highlight php %}

<?php
$I->dontSeeLink('Logout'); // I suppose user is not logged in
$I->dontSeeLink('Checkout now', '/store/cart.php');

{% endhighlight %}


#### dontSeeOptionIsSelected

* `param ` $selector
* `param ` $optionText
* `return mixed|void`

Checks that the given option is not selected.

{% highlight php %}

<?php
$I->dontSeeOptionIsSelected('#form input[name=payment]', 'Visa');

{% endhighlight %}


#### dontSeeRecord

* `part` orm
* `param string` $table
* `param array` $attributes
* `return void`

Checks that record does not exist in database.

You can pass the name of a database table or the class name of an Eloquent model as the first argument.

{% highlight php %}

<?php
$I->dontSeeRecord('users', ['name' => 'davert']);
$I->dontSeeRecord('App\Models\User', ['name' => 'davert']);

{% endhighlight %}


#### dontSeeResponseCodeIs

* `param int` $code
* `return void`

Checks that response code is equal to value provided.

{% highlight php %}

<?php
$I->dontSeeResponseCodeIs(200);

// recommended \Codeception\Util\HttpCode
$I->dontSeeResponseCodeIs(\Codeception\Util\HttpCode::OK);

{% endhighlight %}


#### fillField

* `param ` $field
* `param ` $value
* `return void`

Fills a text field or textarea with the given string.

{% highlight php %}

<?php
$I->fillField("//input[@type='text']", "Hello World!");
$I->fillField(['name' => 'email'], 'jon@example.com');

{% endhighlight %}


#### followRedirect

* `return void`

Follow pending redirect if there is one.

{% highlight php %}

<?php
$I->followRedirect();

{% endhighlight %}


#### getApplication

* `return \Laravel\Lumen\Application`

Provides access the Lumen application object.


#### grabAttributeFrom

* `param ` $cssOrXpath
* `param string` $attribute
* `return mixed`

Grabs the value of the given attribute value from the given element.

Fails if element is not found.

{% highlight php %}

<?php
$I->grabAttributeFrom('#tooltip', 'title');

{% endhighlight %}


#### grabCookie

* `param string` $cookie
* `param array` $params
* `return mixed`

Grabs a cookie value.

You can set additional cookie params like `domain`, `path` in array passed as last argument.
If the cookie is set by an ajax request (XMLHttpRequest), there might be some delay caused by the browser, so try `$I->wait(0.1)`.


#### grabFromCurrentUrl

* `param ?string` $uri
* `return mixed`

Executes the given regular expression against the current URI and returns the first capturing group.

If no parameters are provided, the full URI is returned.

{% highlight php %}

<?php
$user_id = $I->grabFromCurrentUrl('~^/user/(\d+)/~');
$uri = $I->grabFromCurrentUrl();

{% endhighlight %}


#### grabMultiple

* `param ` $cssOrXpath
* `param ?string` $attribute
* `return string[]`

Grabs either the text content, or attribute values, of nodes
matched by $cssOrXpath and returns them as an array.

{% highlight html %}

<a href="#first">First</a>
<a href="#second">Second</a>
<a href="#third">Third</a>

{% endhighlight %}

{% highlight php %}

<?php
// would return ['First', 'Second', 'Third']
$aLinkText = $I->grabMultiple('a');

// would return ['#first', '#second', '#third']
$aLinks = $I->grabMultiple('a', 'href');

{% endhighlight %}


#### grabPageSource

* `throws ModuleException` if no page was opened.
* `return string` Current page source code.

Grabs current page source code.


#### grabRecord

* `part` orm
* `param string` $table
* `param array` $attributes
* `return array|EloquentModel`

Retrieves record from database
If you pass the name of a database table as the first argument, this method returns an array.

You can also pass the class name of an Eloquent model, in that case this method returns an Eloquent model.

{% highlight php %}

<?php
$record = $I->grabRecord('users', ['name' => 'davert']); // returns array
$record = $I->grabRecord('App\Models\User', ['name' => 'davert']); // returns Eloquent model

{% endhighlight %}


#### grabService

* `param string` $class
* `return mixed`

Return an instance of a class from the IoC Container.

Example
{% highlight php %}

<?php
// In Lumen
App::bind('foo', function($app)
{
    return new FooBar;
});

// Then in test
$service = $I->grabService('foo');

// Will return an instance of FooBar, also works for singletons.

{% endhighlight %}


#### grabTextFrom

* `param ` $cssOrXPathOrRegex
* `return mixed`

Finds and returns the text contents of the given element.

If a fuzzy locator is used, the element is found using CSS, XPath,
and by matching the full page source by regular expression.

{% highlight php %}

<?php
$heading = $I->grabTextFrom('h1');
$heading = $I->grabTextFrom('descendant-or-self::h1');
$value = $I->grabTextFrom('~<input value=(.*?)]~sgi'); // match with a regex

{% endhighlight %}


#### grabValueFrom

* `param ` $field
* `return mixed`

Finds the value for the given form field.

If a fuzzy locator is used, the field is found by field name, CSS, and XPath.

{% highlight php %}

<?php
$name = $I->grabValueFrom('Name');
$name = $I->grabValueFrom('input[name=username]');
$name = $I->grabValueFrom('descendant-or-self::form/descendant::input[@name = 'username']');
$name = $I->grabValueFrom(['name' => 'username']);

{% endhighlight %}


#### have

* `part` orm
* `see` https://lumen.laravel.com/docs/master/testing#model-factories
* `param string` $model
* `param array` $attributes
* `param string` $name
* `return mixed`

Use Lumen's model factory to create a model.

{% highlight php %}

<?php
$I->have('App\Models\User');
$I->have('App\Models\User', ['name' => 'John Doe']);
$I->have('App\Models\User', [], 'admin');

{% endhighlight %}


#### haveApplicationHandler

* `param ` $handler
* `return void`

Register a handler than can be used to modify the Laravel application object after it is initialized.

The Laravel application object will be passed as an argument to the handler.

{% highlight php %}

<?php
$I->haveApplicationHandler(function($app) {
    $app->make('config')->set(['test_value' => '10']);
});

{% endhighlight %}


#### haveBinding

* `param ` $abstract
* `param ` $concrete
* `return void`

Add a binding to the Laravel service container.

(https://laravel.com/docs/master/container)

{% highlight php %}

<?php
$I->haveBinding('App\MyInterface', 'App\MyImplementation');

{% endhighlight %}


#### haveContextualBinding

* `param ` $concrete
* `param ` $abstract
* `param ` $implementation
* `return void`

Add a contextual binding to the Laravel service container.

(https://laravel.com/docs/master/container)

{% highlight php %}

<?php
$I->haveContextualBinding('App\MyClass', '$variable', 'value');

// This is similar to the following in your Laravel application
$app->when('App\MyClass')
    ->needs('$variable')
    ->give('value');

{% endhighlight %}


#### haveHttpHeader

* `param string` $name the name of the request header
* `param string` $value the value to set it to for subsequent
       requests
* `return void`

Sets the HTTP header to the passed value - which is used on
subsequent HTTP requests through PhpBrowser.

Example:
{% highlight php %}

<?php
$I->haveHttpHeader('X-Requested-With', 'Codeception');
$I->amOnPage('test-headers.php');

{% endhighlight %}

To use special chars in Header Key use HTML Character Entities:
Example:
Header with underscore - 'Client_Id'
should be represented as - 'Client&#x0005F;Id' or 'Client&#95;Id'

{% highlight php %}

<?php
$I->haveHttpHeader('Client&#95;Id', 'Codeception');

{% endhighlight %}


#### haveInstance

* `param ` $abstract
* `param ` $instance
* `return void`

Add an instance binding to the Laravel service container.

(https://laravel.com/docs/master/container)

{% highlight php %}

<?php
$I->haveInstance('App\MyClass', new App\MyClass());

{% endhighlight %}


#### haveMultiple

* `part` orm
* `see` https://lumen.laravel.com/docs/master/testing#model-factories
* `param string` $model
* `param int` $times
* `param array` $attributes
* `param string` $name
* `return mixed`

Use Laravel model factory to create multiple models.

{% highlight php %}

<?php
$I->haveMultiple('App\Models\User', 10);
$I->haveMultiple('App\Models\User', 10, ['name' => 'John Doe']);
$I->haveMultiple('App\Models\User', 10, [], 'admin');

{% endhighlight %}


#### haveRecord

* `part` orm
* `param ` $table
* `param ` $attributes
* `return integer|EloquentModel`

Inserts record into the database.

If you pass the name of a database table as the first argument, this method returns an integer ID.
You can also pass the class name of an Eloquent model, in that case this method returns an Eloquent model.

{% highlight php %}

<?php
$userId = $I->haveRecord('users', ['name' => 'Davert']); // returns integer
$user = $I->haveRecord('App\Models\User', ['name' => 'Davert']); // returns Eloquent model

{% endhighlight %}


#### haveServerParameter

* `param string` $name
* `param string` $value
* `return void`

Sets SERVER parameter valid for all next requests.

{% highlight php %}

$I->haveServerParameter('name', 'value');

{% endhighlight %}


#### haveSingleton

* `param ` $abstract
* `param ` $concrete
* `return void`

Add a singleton binding to the Laravel service container.

(https://laravel.com/docs/master/container)

{% highlight php %}

<?php
$I->haveSingleton('My\Interface', 'My\Singleton');

{% endhighlight %}


#### make

* `part` orm
* `see` https://lumen.laravel.com/docs/master/testing#model-factories
* `param string` $model
* `param array` $attributes
* `param string` $name
* `return mixed`

Use Lumen's model factory to make a model instance.

{% highlight php %}

<?php
$I->make('App\Models\User');
$I->make('App\Models\User', ['name' => 'John Doe']);
$I->make('App\Models\User', [], 'admin');

{% endhighlight %}


#### makeHtmlSnapshot

* `param ?string` $name
* `return void`

Use this method within an [interactive pause](https://codeception.com/docs/02-GettingStarted#Interactive-Pause) to save the HTML source code of the current page.

{% highlight php %}

<?php
$I->makeHtmlSnapshot('edit_page');
// saved to: tests/_output/debug/edit_page.html
$I->makeHtmlSnapshot();
// saved to: tests/_output/debug/2017-05-26_14-24-11_4b3403665fea6.html

{% endhighlight %}


#### makeMultiple

* `part` orm
* `see` https://lumen.laravel.com/docs/master/testing#model-factories
* `param string` $model
* `param int` $times
* `param array` $attributes
* `param string` $name
* `return mixed`

Use Laravel model factory to make multiple model instances.

{% highlight php %}

<?php
$I->makeMultiple('App\Models\User', 10);
$I->makeMultiple('App\Models\User', 10, ['name' => 'John Doe']);
$I->makeMultiple('App\Models\User', 10, [], 'admin');

{% endhighlight %}


#### moveBack

* `param int` $numberOfSteps (default value 1)
* `return void`

Moves back in history.


#### resetCookie

* `param ` $cookie
* `param ` $params
* `return mixed|void`

Unsets cookie with the given name.

You can set additional cookie params like `domain`, `path` in array passed as last argument.


#### see

* `param array|string` $selector optional
* `param string` $text
* `return void`

Checks that the current page contains the given string (case insensitive).

You can specify a specific HTML element (via CSS or XPath) as the second
parameter to only search within that element.

{% highlight php %}

<?php
$I->see('Logout');                        // I can suppose user is logged in
$I->see('Sign Up', 'h1');                 // I can suppose it's a signup page
$I->see('Sign Up', '//body/h1');          // with XPath
$I->see('Sign Up', ['css' => 'body h1']); // with strict CSS locator

{% endhighlight %}

Note that the search is done after stripping all HTML tags from the body,
so `$I->see('strong')` will return true for strings like:

  - `<p>I am Stronger than thou</p>`
  - `<script>document.createElement('strong');</script>`

But will *not* be true for strings like:

  - `<strong>Home</strong>`
  - `<div class="strong">Home</strong>`
  - `<!-- strong -->`

For checking the raw source code, use `seeInSource()`.


#### seeAuthentication

* `return void`

Checks that user is authenticated.


#### seeCheckboxIsChecked

* `param ` $checkbox
* `return void`

Checks that the specified checkbox is checked.

{% highlight php %}

<?php
$I->seeCheckboxIsChecked('#agree'); // I suppose user agreed to terms
$I->seeCheckboxIsChecked('#signup_form input[type=checkbox]'); // I suppose user agreed to terms, If there is only one checkbox in form.
$I->seeCheckboxIsChecked('//form/input[@type=checkbox and @name=agree]');

{% endhighlight %}


#### seeCookie

* `param ` $cookie
* `param ` $params
* `return mixed|void`

Checks that a cookie with the given name is set.

You can set additional cookie params like `domain`, `path` as array passed in last argument.

{% highlight php %}

<?php
$I->seeCookie('PHPSESSID');

{% endhighlight %}


#### seeCurrentUrlEquals

* `param string` $uri
* `return void`

Checks that the current URL is equal to the given string.

Unlike `seeInCurrentUrl`, this only matches the full URL.

{% highlight php %}

<?php
// to match root url
$I->seeCurrentUrlEquals('/');

{% endhighlight %}


#### seeCurrentUrlMatches

* `param string` $uri
* `return void`

Checks that the current URL matches the given regular expression.

{% highlight php %}

<?php
// to match root url
$I->seeCurrentUrlMatches('~^/users/(\d+)~');

{% endhighlight %}


#### seeElement

* `param ` $selector
* `param array` $attributes
* `return void`

Checks that the given element exists on the page and is visible.

You can also specify expected attributes of this element.

{% highlight php %}

<?php
$I->seeElement('.error');
$I->seeElement('//form/input[1]');
$I->seeElement('input', ['name' => 'login']);
$I->seeElement('input', ['value' => '123456']);

// strict locator in first arg, attributes in second
$I->seeElement(['css' => 'form input'], ['name' => 'login']);

{% endhighlight %}


#### seeInCurrentUrl

* `param string` $uri
* `return void`

Checks that current URI contains the given string.

{% highlight php %}

<?php
// to match: /home/dashboard
$I->seeInCurrentUrl('home');
// to match: /users/1
$I->seeInCurrentUrl('/users/');

{% endhighlight %}


#### seeInField

* `param string|array` $field
* `param ` $value
* `return void`

Checks that the given input field or textarea *equals* (i.e. not just contains) the given value.

Fields are matched by label text, the "name" attribute, CSS, or XPath.

{% highlight php %}

<?php
$I->seeInField('Body','Type your comment here');
$I->seeInField('form textarea[name=body]','Type your comment here');
$I->seeInField('form input[type=hidden]','hidden_value');
$I->seeInField('#searchform input','Search');
$I->seeInField('//form/*[@name=search]','Search');
$I->seeInField(['name' => 'search'], 'Search');

{% endhighlight %}


#### seeInFormFields

* `param ` $formSelector
* `param array` $params
* `return void`

Checks if the array of form parameters (name => value) are set on the form matched with the
passed selector.

{% highlight php %}

<?php
$I->seeInFormFields('form[name=myform]', [
     'input1' => 'value',
     'input2' => 'other value',
]);

{% endhighlight %}

For multi-select elements, or to check values of multiple elements with the same name, an
array may be passed:

{% highlight php %}

<?php
$I->seeInFormFields('.form-class', [
     'multiselect' => [
         'value1',
         'value2',
     ],
     'checkbox[]' => [
         'a checked value',
         'another checked value',
     ],
]);

{% endhighlight %}

Additionally, checkbox values can be checked with a boolean.

{% highlight php %}

<?php
$I->seeInFormFields('#form-id', [
     'checkbox1' => true,        // passes if checked
     'checkbox2' => false,       // passes if unchecked
]);

{% endhighlight %}

Pair this with submitForm for quick testing magic.

{% highlight php %}

<?php
$form = [
     'field1' => 'value',
     'field2' => 'another value',
     'checkbox1' => true,
     // ...
];
$I->submitForm('//form[@id=my-form]', string $form, 'submitButton');
// $I->amOnPage('/path/to/form-page') may be needed
$I->seeInFormFields('//form[@id=my-form]', string $form);

{% endhighlight %}


#### seeInSource

* `param string` $raw
* `return void`

Checks that the current page contains the given string in its
raw source code.

{% highlight php %}

<?php
$I->seeInSource('<h1>Green eggs &amp; ham</h1>');

{% endhighlight %}


#### seeInTitle

* `param ` $title
* `return mixed|void`

Checks that the page title contains the given string.

{% highlight php %}

<?php
$I->seeInTitle('Blog - Post #1');

{% endhighlight %}


#### seeLink

* `param string` $text
* `param ?string` $url
* `return void`

Checks that there's a link with the specified text.

Give a full URL as the second parameter to match links with that exact URL.

{% highlight php %}

<?php
$I->seeLink('Logout'); // matches <a href="#">Logout</a>
$I->seeLink('Logout','/logout'); // matches <a href="/logout">Logout</a>

{% endhighlight %}


#### seeNumberOfElements

* `param int|int[]` $expected
* `param ` $selector
* `return void`

Checks that there are a certain number of elements matched by the given locator on the page.

{% highlight php %}

<?php
$I->seeNumberOfElements('tr', 10);
$I->seeNumberOfElements('tr', [0,10]); // between 0 and 10 elements

{% endhighlight %}


#### seeOptionIsSelected

* `param ` $selector
* `param ` $optionText
* `return mixed|void`

Checks that the given option is selected.

{% highlight php %}

<?php
$I->seeOptionIsSelected('#form input[name=payment]', 'Visa');

{% endhighlight %}


#### seePageNotFound

* `return void`

Asserts that current page has 404 response status code.


#### seeRecord

* `part` orm
* `param string` $table
* `param array` $attributes
* `return void`

Checks that record exists in database.

You can pass the name of a database table or the class name of an Eloquent model as the first argument.

{% highlight php %}

<?php
$I->seeRecord('users', ['name' => 'Davert']);
$I->seeRecord('App\Models\User', ['name' => 'Davert']);
?>

{% endhighlight %}


#### seeResponseCodeIs

* `param int` $code
* `return void`

Checks that response code is equal to value provided.

{% highlight php %}

<?php
$I->seeResponseCodeIs(200);

// recommended \Codeception\Util\HttpCode
$I->seeResponseCodeIs(\Codeception\Util\HttpCode::OK);

{% endhighlight %}


#### seeResponseCodeIsBetween

* `param int` $from
* `param int` $to
* `return void`

Checks that response code is between a certain range. Between actually means [from <= CODE <= to]


#### seeResponseCodeIsClientError

* `return void`

Checks that the response code is 4xx


#### seeResponseCodeIsRedirection

* `return void`

Checks that the response code 3xx


#### seeResponseCodeIsServerError

* `return void`

Checks that the response code is 5xx


#### seeResponseCodeIsSuccessful

* `return void`

Checks that the response code 2xx


#### selectOption

* `param ` $select
* `param ` $option
* `return void`

Selects an option in a select tag or in radio button group.

{% highlight php %}

<?php
$I->selectOption('form select[name=account]', 'Premium');
$I->selectOption('form input[name=payment]', 'Monthly');
$I->selectOption('//form/select[@name=account]', 'Monthly');

{% endhighlight %}

Provide an array for the second argument to select multiple options:

{% highlight php %}

<?php
$I->selectOption('Which OS do you use?', array('Windows','Linux'));

{% endhighlight %}

Or provide an associative array for the second argument to specifically define which selection method should be used:

{% highlight php %}

<?php
$I->selectOption('Which OS do you use?', array('text' => 'Windows')); // Only search by text 'Windows'
$I->selectOption('Which OS do you use?', array('value' => 'windows')); // Only search by value 'windows'

{% endhighlight %}


#### sendAjaxGetRequest

* `param string` $uri
* `param array` $params
* `return void`

Sends an ajax GET request with the passed parameters.

See `sendAjaxPostRequest()`


#### sendAjaxPostRequest

* `param string` $uri
* `param array` $params
* `return void`

Sends an ajax POST request with the passed parameters.

The appropriate HTTP header is added automatically:
`X-Requested-With: XMLHttpRequest`
Example:
{% highlight php %}

<?php
$I->sendAjaxPostRequest('/add-task', ['task' => 'lorem ipsum']);

{% endhighlight %}
Some frameworks (e.g. Symfony) create field names in the form of an "array":
`<input type="text" name="form[task]">`
In this case you need to pass the fields like this:
{% highlight php %}

<?php
$I->sendAjaxPostRequest('/add-task', ['form' => [
    'task' => 'lorem ipsum',
    'category' => 'miscellaneous',
]]);

{% endhighlight %}


#### sendAjaxRequest

* `param string` $method
* `param string` $uri
* `param array` $params
* `return void`

Sends an ajax request, using the passed HTTP method.

See `sendAjaxPostRequest()`
Example:
{% highlight php %}

<?php
$I->sendAjaxRequest('PUT', '/posts/7', ['title' => 'new title']);

{% endhighlight %}


#### setApplication

* `param \Laravel\Lumen\Application` $app
* `return void`


#### setCookie

* `param ` $name
* `param ` $val
* `param ` $params
* `return mixed|void`

Sets a cookie with the given name and value.

You can set additional cookie params like `domain`, `path`, `expires`, `secure` in array passed as last argument.

{% highlight php %}

<?php
$I->setCookie('PHPSESSID', 'el4ukv0kqbvoirg7nkp4dncpk3');

{% endhighlight %}


#### setMaxRedirects

* `param int` $maxRedirects
* `return void`

Sets the maximum number of redirects that the Client can follow.

{% highlight php %}

<?php
$I->setMaxRedirects(2);

{% endhighlight %}


#### setServerParameters

* `param array` $params
* `return void`

Sets SERVER parameters valid for all next requests.

this will remove old ones.

{% highlight php %}

$I->setServerParameters([]);

{% endhighlight %}


#### startFollowingRedirects

* `return void`

Enables automatic redirects to be followed by the client.

{% highlight php %}

<?php
$I->startFollowingRedirects();

{% endhighlight %}


#### stopFollowingRedirects

* `return void`

Prevents automatic redirects to be followed by the client.

{% highlight php %}

<?php
$I->stopFollowingRedirects();

{% endhighlight %}


#### submitForm

* `param ` $selector
* `param array` $params
* `param ?string` $button
* `return void`

Submits the given form on the page, with the given form
values.  Pass the form field's values as an array in the second
parameter.

Although this function can be used as a short-hand version of
`fillField()`, `selectOption()`, `click()` etc. it has some important
differences:

 * Only field *names* may be used, not CSS/XPath selectors nor field labels
 * If a field is sent to this function that does *not* exist on the page,
   it will silently be added to the HTTP request.  This is helpful for testing
   some types of forms, but be aware that you will *not* get an exception
   like you would if you called `fillField()` or `selectOption()` with
   a missing field.

Fields that are not provided will be filled by their values from the page,
or from any previous calls to `fillField()`, `selectOption()` etc.
You don't need to click the 'Submit' button afterwards.
This command itself triggers the request to form's action.

You can optionally specify which button's value to include
in the request with the last parameter (as an alternative to
explicitly setting its value in the second parameter), as
button values are not otherwise included in the request.

Examples:

{% highlight php %}

<?php
$I->submitForm('#login', [
    'login' => 'davert',
    'password' => '123456'
]);
// or
$I->submitForm('#login', [
    'login' => 'davert',
    'password' => '123456'
], 'submitButtonName');


{% endhighlight %}

For example, given this sample "Sign Up" form:

{% highlight html %}

<form id="userForm">
    Login:
    <input type="text" name="user[login]" /><br/>
    Password:
    <input type="password" name="user[password]" /><br/>
    Do you agree to our terms?
    <input type="checkbox" name="user[agree]" /><br/>
    Subscribe to our newsletter?
    <input type="checkbox" name="user[newsletter]" value="1" checked="checked" /><br/>
    Select pricing plan:
    <select name="plan">
        <option value="1">Free</option>
        <option value="2" selected="selected">Paid</option>
    </select>
    <input type="submit" name="submitButton" value="Submit" />
</form>

{% endhighlight %}

You could write the following to submit it:

{% highlight php %}

<?php
$I->submitForm(
    '#userForm',
    [
        'user' => [
            'login' => 'Davert',
            'password' => '123456',
            'agree' => true
        ]
    ],
    'submitButton'
);

{% endhighlight %}
Note that "2" will be the submitted value for the "plan" field, as it is
the selected option.

To uncheck the pre-checked checkbox "newsletter", call `$I->uncheckOption(['name' => 'user[newsletter]']);` *before*,
then submit the form as shown here (i.e. without the "newsletter" field in the `$params` array).

You can also emulate a JavaScript submission by not specifying any
buttons in the third parameter to submitForm.

{% highlight php %}

<?php
$I->submitForm(
    '#userForm',
    [
        'user' => [
            'login' => 'Davert',
            'password' => '123456',
            'agree' => true
        ]
    ]
);

{% endhighlight %}

This function works well when paired with `seeInFormFields()`
for quickly testing CRUD interfaces and form validation logic.

{% highlight php %}

<?php
$form = [
     'field1' => 'value',
     'field2' => 'another value',
     'checkbox1' => true,
     // ...
];
$I->submitForm('#my-form', $form, 'submitButton');
// $I->amOnPage('/path/to/form-page') may be needed
$I->seeInFormFields('#my-form', $form);

{% endhighlight %}

Parameter values can be set to arrays for multiple input fields
of the same name, or multi-select combo boxes.  For checkboxes,
you can use either the string value or boolean `true`/`false` which will
be replaced by the checkbox's value in the DOM.

{% highlight php %}

<?php
$I->submitForm('#my-form', [
     'field1' => 'value',
     'checkbox' => [
         'value of first checkbox',
         'value of second checkbox',
     ],
     'otherCheckboxes' => [
         true,
         false,
         false
     ],
     'multiselect' => [
         'first option value',
         'second option value'
     ]
]);

{% endhighlight %}

Mixing string and boolean values for a checkbox's value is not supported
and may produce unexpected results.

Field names ending in `[]` must be passed without the trailing square
bracket characters, and must contain an array for its value.  This allows
submitting multiple values with the same name, consider:

{% highlight php %}

<?php
// This will NOT work correctly
$I->submitForm('#my-form', [
    'field[]' => 'value',
    'field[]' => 'another value',  // 'field[]' is already a defined key
]);

{% endhighlight %}

The solution is to pass an array value:

{% highlight php %}

<?php
// This way both values are submitted
$I->submitForm('#my-form', [
    'field' => [
        'value',
        'another value',
    ]
]);

{% endhighlight %}


#### switchToIframe

* `param string` $name
* `return void`

Switch to iframe or frame on the page.

Example:
{% highlight html %}

<iframe name="another_frame" src="http://example.com">

{% endhighlight %}

{% highlight php %}

<?php
# switch to iframe
$I->switchToIframe("another_frame");

{% endhighlight %}


#### uncheckOption

* `param ` $option
* `return void`

Unticks a checkbox.

{% highlight php %}

<?php
$I->uncheckOption('#notify');

{% endhighlight %}

<p>&nbsp;</p><div class="alert alert-warning">Module reference is taken from the source code. <a href="https://github.com/Codeception/module-lumen/tree/master/src/Codeception/Module/Lumen.php">Help us to improve documentation. Edit module reference</a></div>
