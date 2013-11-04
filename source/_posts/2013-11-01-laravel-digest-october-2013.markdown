---
layout: post
categories: [Laravel, Laravel Digest, Code]
date: 2013-11-01 13:20
comments: false
title: "Laravel Digest (October 2013)"
---

Welcome to the second instalment of Laravel Digest, the series where I give a regular rundown of important changes, fixes and additions to Laravel's master branch. Below are the changes up to the end of October 2013:

- Blade braces can now be escaped using `@` which is useful for people using clientside templating languages that also use the braces ([`cb93bf3`](https://github.com/laravel/framework/commit/cb93bf3df8d25c04939462f81b75ddb9e4e6faa0)):

The following code will output `hello`
``` html
{% raw %}{{ 'hello' }}{% endraw %}
```

The following code will output `{% raw %}{{ 'hello' }}{% endraw %}`
``` html
{% raw %}@{{ $variable }}{% endraw %}
```

Use case, using handlebars.js:
``` html
{% raw %}<div class="name">@{{ user.name }}</div>{% endraw %}
```

- Queued cookies - because cookies can only be attached to a request, using `Cookie::set()` before the request was instantiated previous lost the cookie (or simply wasn't allowed). Now `Cookie::queue()` can be used to queue cookies for eventual sending with the request.

- `Route::controller` is back and appears slightly cleverer than before in that it first seeks out all callable methods on the given controller and adds each route individually, then sets up a 'default' route for anything it can't detect ([`98c14a9`](https://github.com/laravel/framework/commit/98c14a9f90ada608418490e4cfacd51f4ebda384))

    - `Route::controllers` can also be used to hook multiple controllers up in one go by passing an associative array of uri to controller FQCN

- `App::env()` can now be used to check the environment against a list provided (array or multiple arguments) ([`66e5a0b`](https://github.com/laravel/framework/commit/66e5a0b32f61b1ec0e14aa4bc4219d15c2a247c7))

- Remote tail command (`artisan tail`) allows tailing of a remote log file (assuming the remote connection is set up correctly in the config and the remote project uses a single log file) ([`25b1cac`](https://github.com/laravel/framework/commit/25b1cac27080641b289c2ad791b734efff92bcef))
    - As a consequence of this work, the logging system was simplified down to a single log file by default (no daily rotation and no SAPI name, this looks like it may generate rather large log files to me, I'd have preferred to keep daily rotation) ([`laravel/laravel@088f4b6`](https://github.com/laravel/laravel/commit/088f4b69b6b9846dd54b55688f11e44b9bc73483))
    - Also as a consequence, the live debugging through sockets was removed. Now the `tail` command will tail a remote log file if there's a remote connection specified, or tail the local log file if there's no remote connection specified ([`f62d1a7`](https://github.com/laravel/framework/commit/f62d1a727155073357babee95a8f679003a8b93c)/[`763a0265`](https://github.com/laravel/framework/commit/763a02652ba23a9b61f59b47c05ab146bbe76136))

- New `Router::input()` method that allows you to get the value of a named route parameter ([`28e36bb`](https://github.com/laravel/framework/commit/28e36bbc0296e3b4cdf5ae18c5a972923c05b4de))

- Controller filters can now be added as controller functions using the `@` syntax as a shorthand (see [issue 2432](https://github.com/laravel/framework/issues/2432)) ([`d0e0c63`](https://github.com/laravel/framework/commit/d0e0c632e2b3ff15746e48ec7bd57c5cc51d0ae0)):

``` php
<?php

class UserController extends Contoller
{
    public function __construct()
    {
        // before:
        $this->beforeFilter(function ($route) {
            return $this->hasPermission($route);
        }, ['only' => 'edit']);

        // after, shorthand
        $this->beforeFilter('@hasPermission', ['only' => 'edit']);
    }

    public function hasPermission($route)
    {
        $id = $route->getParameter('id');

        if (! Auth::user()->ownsPost($id)) {
            return Response::make('You are not authorised to edit this post', 401);
        }
    }
}
```

- Can now pass DateTime and Carbon instances to Cache method that accept a duration (in this case, the time difference between now and the time passed is calculated in minutes) ([`977f803`](https://github.com/laravel/framework/commit/977f803030b84ce28f034bcf925f93bfb029a0d4))

- Now uses [Stack](http://stackphp.com/) for middleware (multiple commits). Repercussions:

    - `close` application hook is now deprecated in favour of using a stack middleware ([`18e1aec`](https://github.com/laravel/framework/commit/18e1aecb69897b9533968d4c9f291f3a522ef98f))
    - Presumably more...

- Should now use Input::cookie for cookies, not Cookie::get ([`e0fe79e`](https://github.com/laravel/framework/commit/e0fe79e398003e54d54f2626e1283e97209b7f50) removed `Cookie::get()` and [`58ec2fe`](https://github.com/laravel/framework/commit/58ec2fe05f092bdd09f2e05d8a86633b97417cef) for temporary deprecation code in the facade)

- If using MySQL, `UPDATE` queries can now have `orderBy` and `limit` specified ([`d9d61e1`](https://github.com/laravel/framework/commit/d9d61e13fc2f01efc00c9b87ff6f1e91bab1e9b8))

- As with overriding packages' config and views, translations can now be overridden using a similar convention ([`5483042`](https://github.com/laravel/framework/commit/5483042e8762d4f8ffc356579ddaae4f7dc3cbc9))

    - It should be noted that the convention is not the same: it is not `app/lang/packages/vendor/package/` but uses the namespace (i.e. package name) *only*, like when referencing views/config (i.e. `View::make('namespace:path.view')` and `Config:get('namespace:config.key')`)

    - Files for a package `vendor/package` in locale `en` should go in `app/lang/packages/en/package/` -- see how it's just `package` not `vendor/package` like it would be for config and views

    - So to reiterate, for a package called `vendor/package`:

        - Views: `View::make('package::some/view')` ⟶ `app/views/packages/vendor/package/some/view{.blade}.php`
        - Config: `Config::get('package::some.key')` ⟶ `app/config/packages/vendor/package/some.php`, then key `key`
        - Translations: `Lang::get('package::some.key')` ⟶ `app/lang/packages/en/package/some.php`, then key `key`

- You can now use `Auth::viaRemember()` to determine whether the logged-in user was authorised through the 'remember me' cookie or not ([`2184ad4`](https://github.com/laravel/framework/commit/2184ad4c8fe5f05b89ae1d5c6f87012cca150101))

- New `bindShared` method on the IoC container which should now be used in place of the old share syntax ([`07233b3`](https://github.com/laravel/framework/commit/07233b32eb190dffde427b3aee1e5e4f855abd00)):

``` php
// this
$this->app['my.key'] = $this->app->share(function ($app) {
    return new MyClass();
});

// becomes this
$this->app->bindShared('my.key', function ($app) {
    return new MyClass();
});
```

- `ServiceProvider` no longer uses very weird logic to guess a package's namespace. This probably won't affect many people but I'm pointing it out as it has affected me and it's good to know for future reference ([`6089525`](https://github.com/laravel/framework/commit/6089525e1293407085c07588f2eb8b2cd8644b01))

- You can now pass a view to `Paginator::links([$view])` in order to override the default view for that call ([`9d1150c`](https://github.com/laravel/framework/commit/9d1150c8851d22cb1a14faa35304b3dc7f17c94b))

- New `hasManyThrough` relation type ([`0925d86`](https://github.com/laravel/framework/commit/0925d868b900372a39c0f9985308004d24bad46d))
    - There's no real documentation on this yet and the tests are a little cryptic, so I'll talk more about this in the next update.

- `Validator::make` can now take a fourth parameter that specified the custom parameters for the validator. Presumably to be used by custom validators. ([`93977e4`](https://github.com/laravel/framework/commit/93977e49559ec82e8a82f19bbdfcf4f27cfb4ac0))

- IoC container old `resolving` method is now renamed to `resolvingAny` and now there's a new `resolving` method in its place that accepts an abstract object type to only be fired when that abstract type is being resolved ([`aa6df0a`](https://github.com/laravel/framework/commit/aa6df0aa52f588c588c2f0c0646039ad35efe19b))

- Any alaises bound to a given IoC key will now be removed when rebinding the key ([`8f240f8`](https://github.com/laravel/framework/commit/8f240f83785e1aebd0d5abf848d8425786a9ba86))

- Queue workers will no no longer run if the application is in maintenance mode ([`a8cdb68`](https://github.com/laravel/framework/commit/a8cdb683a097e04281ada784d9073c8a83f72bf1))