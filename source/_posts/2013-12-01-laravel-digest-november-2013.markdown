---
layout: post
categories: [Laravel, Laravel Digest, Code]
date: 2013-12-01 10:00
comments: false
title: "Laravel Digest (November 2013)"
---

Welcome to the third instalment of Laravel Digest, the series where I give a regular rundown of important changes, fixes and additions to Laravel's master branch. Below are the changes up to the end of November 2013:

- Relationships can now reference any key on the parent model using a new last parameter for each of the `\Eloquent\Model` methods (`belongsTo`: [`c7d2740`](https://github.com/laravel/framework/commit/c7d2740404251d9e08a24eacbbc42812b038fbaa), `hasOne`, `hasMany`, morph relationships: [`a634f45`](https://github.com/laravel/framework/commit/a634f4a634f457860c8ac7c091da4912351dcdc29562c15)):

``` php
<?php

class User extends Eloquent
{
    // belongsTo
    public function group()
    {
        return $this->belongsTo('Group', 'group', 'name');
    }

    // hasOne
    public function city()
    {
        return $this->hasOne('City', 'city', 'name');
    }
    
    // hasMany
    public function roles()
    {
        return $this->hasMany('Role', 'role', 'type');
    }

    // etc...
}
```

- `Builder#has` method now maintains a query's constraints ([`7196279`](https://github.com/laravel/framework/commit/7196279d1f6c5a9e067beb6f39fea5eaa50e7c19))

- Scoped queries now return the value from the scope method for better chaining ([`23adaa4`](https://github.com/laravel/framework/commit/23adaa4068ad91fd934b58b6f60cef06135a76e3))
    - In case you're confused how this is new functionality (I was), previously, the magic method that called your custom scope method ignored what you returned and instead returned the `Builder` instance (which to be honest is the 95%+ use case anyway), but it now returns what you actually return from your scope method (so if you weren't already, make sure you return the `Builder` instance!)

- `has` queries got even better: new `whereHas()` and `orWhereHas()` that allow you to set specific constraints on the `has` relation ([`a634f45`](https://github.com/laravel/framework/commit/a634f45)):

``` php
<?php
// get all users with posts pre-attached, but only users with
// at least one post that is more than a year old
User::with('posts')->whereHas('posts', function ($query) {
    $query->where('created_at', '<', Carbon::now()->subYears(1));
})->get();
```

I'm not sure right now if the `whereHas` affects the `with` (i.e. what actually gets returned pre-attached) or just the mechanism for filtering the original model by the relationship. My guess is the latter.

- New session configuration option `secure` (boolean) that will affect whether the session cookie gets sent via HTTPS only or not ([`37fa9a1`](https://github.com/laravel/framework/commit/37fa9a1c599069ea475e75cd540b9239f76ecc71) and [`laravel/laravel@f387853`](https://github.com/laravel/laravel/commit/

- New short variable defaults in Eloquent ([`10f5bfa`](https://github.com/laravel/framework/commit/10f5bfae60a799b1f7862cb923e7cf2ecd086816))

``` html
<!-- this -->
<div>Hello, {{ isset($username) ? $username : 'anonymous' }}.</div>
<!-- can now be done like this -->
<div>Hello, {{ $username or 'anonymous' }}.</div>
```

- Cache entries can now be tagged. New functionality includes using `Cache::tags` to get a list of the tags used in the cache, and items can be flushed by tag ([`70108fe`](https://github.com/laravel/framework/commit/70108fe4f456f343325234063aa77b9012ede7dd))

- Laravel now sends `X-frame-Options: SAMEORIGIN` by default in the HTTP headers ([`15513cc`](https://github.com/laravel/framework/commit/15513cc96790e33b7a136db381b06805863d3009))

- `belongsToMany` relations now have a firstOrFail method ([`b162579`](https://github.com/laravel/framework/commit/b162579b3911f59f5ca175d2d90d0a221cf6b109))

- Schema builder now has `nullableTimestamps` method that creates timestamps as normal but makes them nullable fields ([`a44fbab`](https://github.com/laravel/framework/commit/a44fbab722c276fca0b09d7a22e446b7894bee93))

- Joins can now have complex conditions if `joinWhere` or `leftJoinWhere` are used ([`a644ea0`](https://github.com/laravel/framework/commit/a644ea01fc3110b29a8f3a9ac96e039b3478953c))

- New `required_without_all` validation rule that works like `required_without` but checks that at least one of the specified fields is present rather than all fields ([`93c1045`](https://github.com/laravel/framework/commit/93c1045fdea4d115d4a9dbc7ae64793bc7063d52))

- Original method that was attempted is passed to `Controller#missingMethod` now as the first parameter, with the additional parameters as an array as the second parameter ([`de871fe`](https://github.com/laravel/framework/commit/de871fe7db2798071e8ca877d04ba7e6629f9f18))

- New Blade `@append` method ([`f0ae98b`](https://github.com/laravel/framework/commit/f0ae98bfd0b07be739eaf9b9bf3505e916d25ca7)):

``` html
@section('content')
Some content for the content section
@append

...

@section('content')
Some more content for the content section
@append
```

- New query builder `rememberForever` method ([`c681584`](https://github.com/laravel/framework/commit/c6815845956e1cbd7039cefe67157ff0b3e8c557))

- New Eloquent model `booting` and `booted` methods ([`2dfb383`](https://github.com/laravel/framework/commit/2dfb3831d62f4689b9e234a83af46551df273609))

- `View#withErrors` now accepts an optional parameter that is a object that implements `MessageProviderInterface` (i.e. can be asked for a `MessageBag`)([`3c5ca1a`](https://github.com/laravel/framework/commit/3c5ca1a85dce7fc915c19b3cd197ad03433375b6))
    - Use case here is for validators: `View::make('some.view')->withErrors($validator);`

- A big change to password resetting/reminder stuff ([`f86d5ea`](https://github.com/laravel/framework/commit/f86d5ea61f6adc2004b8ed259a62cc8008d08fd0))
    - [Related documentation](TODO)

- `Collection#splice` now returns the extracted elements (if any) as a new `Collection` ([`32f107d`](https://github.com/laravel/framework/commit/32f107dae8c052cc012c272e4d2b2f278b86aade))

- Commas are now allowed in route wildcards ([`fdd378b`](https://github.com/laravel/framework/commit/fdd378bb072d6993be9798aa1772c394e65e5048))