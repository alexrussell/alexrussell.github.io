---
layout: post
categories: [Laravel, Laravel Digest, Code]
date: 2013-10-04 10:55
comments: false
title: "Laravel Digest (September 2013)"
---

Introducing Laravel digest, where I let you know what's changed in the most recent commits to Laravel. I like to think of it as me reading the commitlog so you don't have to. The general idea is that I'll list out any notable changes on the master branch (although really I'm looking at both master and 4.0) since the previous update. I pretty much decide what's classed as notable, but usually it'll be stuff that actually affects the way developers work, so small bugfixes won't be in, but new features or uses of existing components are included.

As this is the first installment of Laravel digest there's no "since last time" so I'm arbitrarily deciding its since about mid-September. A fortnight seems like a good period of time between updates (and maybe longer where Taylor has less time to spend on the framework).

So, without further ado:

Notable recent changes (since approx. 15 September 2013) on master specifically (but most apply to 4.0 too):

- `artisan tinker` is now a full REPL using [Boris](https://github.com/d11wtq/boris) ([`5e8d2c0`](https://github.com/laravel/framework/commit/5e8d2c0e38b9630cf881336cbfde6c64657e4d30))
    - It does, however, require `ext_readline` and `ext_posix` if you want to use it though (ouch!)

- Views can have data added eloquently ([`e7fd520`](https://github.com/laravel/framework/commit/e7fd520a8b7d38b9aa68aee8fea880b725da46e9))

``` php
<?php

// this
return View::make('view.name')->with(['user' => $user]);

// or this
return View::make('view.name', ['user' => $user]);

// becomes this
return View::make('view.name')->withUser($user);
```

- View composers can have a priority attached so they run in a given order ([`fb4fe45`](https://github.com/laravel/framework/commit/fb4fe45a9c795e250d046e87f6c3c86a6d01f850))

- Developers can specify a protected instance `dates` array in their Eloquent models that returns an array of fields that should be treated like dates ([`f15b034`](https://github.com/laravel/framework/commit/f15b034dc744290a08e0abc776f06ed991426d4e))

- Query builder/Eloquent now has a chunk method that allows for working through large datasets without killing RAM usage ([`11d3e98`](https://github.com/laravel/framework/commit/11d3e9850dc6139850d5e81c71067ae2d7a894d9)):

``` php
<?php

User::whereActive('1')->chunk(
    100, // per chunk
    function($results) {
        // do something with set of 100 results at a time
    }
);
```

- `Route::group` config array now accepts a `namespace` string that will automatically prefix controller names with the namespace - they can even be nested ([`3d0400e`](https://github.com/laravel/framework/commit/3d0400e55a81b79d3352670f2e24f7358eb95d10))

``` php
<?php

// something a bit like this
Route::group(
    ['prefix' => 'admin'],
    function() {
        Route::get('some/url', 'MyApp\\Admin\\UserController@index');
        Route::get('some/other/url', 'MyApp\\Admin\\UserController@edit');
    }
);

// can become the slightly cleaner
Route::group(
    ['prefix' => 'admin', 'namespace' => 'MyApp\\Admin'],
    function() {
        Route::get('users', 'UserController@index');
        Route::get('user/edit', 'UserController@edit');
    }
);
```

- Query builder/Eloquent can now find multiple rows with `find()` by passing an array ([`911e08b`](https://github.com/laravel/framework/commit/911e08b1501c45b7b58a62007708bc9494e385b5))

``` php
<?php

// get users 1 and 5
$users = User::find([1, 5]);
```

- Can get current environment using `artisan env` (useful for debugging weird database connection issues with artisan - not so useful for knowing what environment the web site is running as artisan and the running web instance will use different methods to determine the environment, so they could quite easily be different) ([`5c7ba12`](https://github.com/laravel/framework/commit/5c7ba12fda225a295ae96dbb7770f4859aa65330))

- Can now return an ArrayObject from a route action and it'll be serialised as JSON (previously it was just just bare arrays and JsonableInterface objects) ([`bd4b0c6`](https://github.com/laravel/framework/commit/bd4b0c6eb5633c8ebf6daf7ed2329550fb2761d5))

- Packages' views can now be published (like config and assets were) using `artisan view:publish` ([`44a27da`](https://github.com/laravel/framework/commit/44a27da7245b5da88fd97934d250b59813127053))

- Paginator can now be returned from a route action and it'll be serialised to JSON (good for paginated responses in APIs) ([`5875b60`](https://github.com/laravel/framework/commit/5875b60d823b331ee838ebbe74fbc64a53c520a4))

- Eloquent query builder now has `firstOrNew` and `firstOrCreate` methods ([`fce86bc`](https://github.com/laravel/framework/commit/fce86bc9b53cb693284ea1dc557b3034375fd1ca)):

    - `firstOrNew` tries to use the passed array of attributes to find the model, and if it can't find it it'll create a new (unsaved) instance of the model
    - `firstOrCreate` tries to use the passed array of attributes to find the model, and if it can't find it it'll create a new instance of the model and save it immediately
    - **Implementation note:** this also uses a convenient new method `firstByAttributes` which is unfortunately protected in `Illuminate\Database\Eloquent\Model`. The method is nothing special, just cycles through the array adding `where`s to a query builder

- new `Redirect::away` method allows passing of any URL - the passed URL is not validated, works just like `Redirect::to` otherwise ([`8c47d11`](https://github.com/laravel/framework/commit/8c47d1173a8f666d8be278219f63d3c51459379e))

- Remote feature now allows setting the port ([`3f7c47c`](https://github.com/laravel/framework/commit/3f7c47cb243be3ba201bf75c6c0c38e7b96b43b3))

- Like the Eloquent view data from `e7fd520`, redirects can do the same ([`97bbfe6`](https://github.com/laravel/framework/commit/97bbfe6f09e7b0a5143d65568e886f7a1ace2f9c)):

``` php
<?php

Redirect::route('login')->withMessage('Please log in');
```

- Validator now has a `sometimes` method that allows to set conditional rules ([`ffa70d3`](https://github.com/laravel/framework/commit/ffa70d312c8e639ce36e2859d4977dba123a2af6), [docs](http://laravel.com/docs/validation#conditionally-adding-rules)):

``` php
<?php

// say we're validating some kind of user type - if 'business_type' is set
// to 'business' then 'business_name' is requied

$v = Validator::make($data, [
    'email'        => 'required|email',
    'name'         => 'required',
    'license_type' => 'required',
]);

// this rule only applies when the closure returns true
$v->sometimes('business_name', 'required', function($input) {
    return $input->license_type == 'business';
});
```

The first parameter to `sometimes` can be an array that specifies multiple rules:

``` php
<?php

// Example: in the checkout area of an ecommerce site, only require the delivery
// fields to be set if the 'deliver to my billing address' field is not checked

$v->sometimes(
    ['delivery_name', 'delivery_address', 'delivery_country' /* etc. */],
    'required',
    function($input) {
        return $input->deliver_to_billing_address != 'yes';
    }
);
```
