---
layout: post
categories: [Laravel, Laravel Digest, Code]
date: 2013-12-15 10:00
comments: false
title: "Laravel Digest (December 2013)"
---

Welcome to the fourth instalment of Laravel Digest, the series where I give a regular rundown of important changes, fixes and additions to Laravel's master branch. Due to the release of Laravel 4.1 I've decided to bring this post forward to match the status of Laravel on the day(ish) of release. As such, below are the changes up to the middle of December 2013:

- `Collection#first` can now have a closure (and default) passed to it that will be passed into a `array_first`-like call ([`d600ebe`](https://github.com/laravel/framework/commit/d600ebe7c2e76ca98c5ed280d77107a4159acfc4)):

``` php
<?php

// get all users
$users = User::all();

// get first that is enabled or string 'None' if none match
$firstEnabled = $users->first(function ($key, $model) {
    return $model->enabled == 1;
}, 'None'); // obviously an awful example in terms of what it returns

```

- New `array_last` helper (side-effect of some other work which was reverted) that works the same as array_first ([`88421dd`](https://github.com/laravel/framework/commit/88421ddc6b2313ec16cd65b66a03ee8258e02702))

- `Collection#sortBy` now takes [sort flags](http://www.php.net/manual/en/function.sort.php) as its second parameter ([`426c9e0`](https://github.com/laravel/framework/commit/426c9e0e39915eedd2861b147ba6701443136e3d))

- You can now retrieve failed recipients to `Mail::send()` with `Mail::failures()` ([a0086a5](https://github.com/laravel/framework/commit/a0086a5779c21a06def56d9cff9bb51a3ed3f242))

- `Model::destroy()` now returns the number of rows that were deleted ([`0f1a3cc`](https://github.com/laravel/framework/commit/0f1a3cce2c08cee6fcea5fd754efe54b1130f4db))

- There's also a bunch of new documentation for all the new features I've spoken about:
    - [Upgrade guide](http://laravel.com/docs/upgrade)
    - [New password reminder system](http://laravel.com/docs/security#password-reminders-and-reset)
    - [Cache tags](http://laravel.com/docs/cache#cache-tags)
    - Queues had a number of changes, the most important being logging of failed jobs. So [its documentation](http://laravel.com/docs/queues#failed-jobs) should be looked over.
    - A [new SSH module](http://laravel.com/docs/ssh) was incorporated primarily for interacting with a live environment (for example, deployment or tailing remote logs).
    - [New Blade features](http://laravel.com/docs/templates#other-blade-control-structures)
    - `Validator#sometimes()` [documentation](http://laravel.com/docs/validation#conditionally-adding-rules)
    - Database [read/write connection splitting](http://laravel.com/docs/database#read-write-connections)
    - Database query builder got [some extra methods as pessimistic locking](http://laravel.com/docs/queries).
    - Eloquent relationships had a few updates too:
        - [hasManyThrough](http://laravel.com/docs/eloquent#has-many-through)
        - [Polymorphic many-to-many()](https://github.com/laravel/docs/pull/547) (not yet in official documentation)
        - [Model::has() and Model:whereHas()](http://laravel.com/docs/eloquent#querying-relations)
        - [Eager loading constraints](http://laravel.com/docs/eloquent#eager-loading) (scroll down a little)
