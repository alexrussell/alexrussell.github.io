---
layout: post
categories: [Laravel, Laravel Digest, Code]
date: 2013-11-01 13:20
comments: false
title: "Laravel Digest (October 2013)"
---

Welcome to the second instalment of Laravel Digest, the series where I give a regular rundown of important changes, fixes and additions to Laravel's master branch. Below are the changes up to the end of October 2013

- Blade braces can now be escaped using `@` which is useful for people using clientside templating languages that also use the braces ([`cb93bf3`](link)):

The following code will output `hello`
``` blade
{{ 'hello' }}
```

The following code will output `{{ 'hello' }}`
``` blade
@{{ $variable }}
```

Use case, using handlebars.js:
``` blade
<div class="name">@{{ user.name }}</div>
```

- Queued cookies - TODO

- `Route::controller` is back and appears slightly cleverer than before in that it first seeks out all callable methods on the given controller and adds each route individually, then sets up a 'default' route for anything it can't detect ([`98c14a9`](link))

    - `Route::controllers` can also be used to hook multiple controllers up in one go by passing an associative array of uri to controller FQCN

- `App::env()` can now be used to check the environment against a list provided (array or multiple arguments) ([`66e5a0b`](link))

- Remote tail command (`artisan tail`) allows tailing of a remote log file (assuming the remote connection is set up correctly in the config and the remote project uses a single log file) ([`25b1cac`](link))
    - As a consequence of this work, the logging system was simplified down to a single log file by default (no daily rotation and no SAPI name, this looks like it may generate rather large log files to me, I'd have preferred to keep daily rotation) ([`laravel/laravel@088f4b6`](https://github.com/laravel/laravel/commit/088f4b69b6b9846dd54b55688f11e44b9bc73483))
    - Also as a consequence, the live debugging through sockets was removed. Now the `tail` command will tail a remote log file if there's a remote connection specified, or tail the local log file if there's no remote connection specified ([`f62d1a7`](link)/[`763a0265`](link))

- Router input method? (This is on the changelog but I don't know what it refers to) - TODO

- Controller filters can now be added as controller functions using the `@` syntax as a shorthand (see [issue 2432](https://github.com/laravel/framework/issues/2432)) ([`d0e0c63`](link)):

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

- Can now pass DateTime and Carbon instances to Cache method that accept a duration (in this case, the time difference between now and the time passed is calculated in minutes) ([`977f803`](link))

- Now uses [Stack](http://stackphp.com/) for middleware (multiple commits). Repercussions:

    - `close` application hook is now deprecated in favour of using a stack middleware ([`18e1aec`](link))
    - probably many more

- Should now use Input::cookie for cookies, not Cookie::get ([`e0fe79e`](link) removed `Cookie::get()` and [`58ec2fe`](link) for temporary deprecation code in the facade)

- If using MySQL, `UPDATE` queries can now have `orderBy` and `limit` specified ([`d9d61e1`](link))

- As with overriding packages' config and views, translations can now be overridden using a similar convention ([`5483042`](link))

    - It should be noted that the convention is not the same: it is not `app/lang/packages/vendor/package/` but uses the namespace (i.e. package name) *only*, like when referencing views/config (i.e. `View::make('namespace:path.view')` and `Config:get('namespace:config.key')`)

    - Files for a package `vendor/package` in locale `en` should go in `app/lang/packages/en/package/` -- see how it's just `package` not `vendor/package` like it would be for config and views

    - So to reiterate, for a package called `vendor/package`:

        - Views: `View::make('package::some/view')` ⟶ `app/views/packages/vendor/package/some/view{.blade}.php`
        - Config: `Config::get('package::some.key')` ⟶ `app/config/packages/vendor/package/some.php`, then key `key`
        - Translations: `Lang::get('package::some.key')` ⟶ `app/lang/packages/en/package/some.php`, then key `key`

- You can now use `Auth::viaRemember()` to determine whether the logged-in user was authorised through the 'remember me' cookie or not ([`2184ad4`](link))

- New `bindShared` method on the IoC container which should now be used in place of the old share syntax (['`077233b3`](link)):

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

- `ServiceProvider` no longer uses very weird logic to guess a package's namespace. This probably won't affect many people but I'm pointing it out as it has affected me and it's good to know for future reference ([`6089525`](link))

- You can now pass a view to `Paginator::links([$view])` in order to override the default view for that call ([`9d1150c`](link))