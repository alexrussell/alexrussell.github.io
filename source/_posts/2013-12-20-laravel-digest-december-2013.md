---
layout: post
categories: [Laravel, Laravel Digest, Code]
date: 2013-12-20 10:00
comments: false
title: "Laravel Digest (December 2013)"
---

Welcome to the fourth instalment of Laravel Digest, the series where I give a regular rundown of important changes, fixes and additions to Laravel's master branch. Due to the release of Laravel 4.1 I've decided to bring this port forward to match the status of Laravel on the day of release. As such, below are the changes up to the middle of December 2013:

- `Collection#first` can now have a closure (and default) passed to it that will be passed into a `array_first`-like call ([`d600ebe`](d600ebe7c2e76ca98c5ed280d77107a4159acfc4)):

``` php
<?php

// get all users
$users = User::all();

// get first that is enabled or string 'None' if none match
$firstEnabled = $users->first(function ($key, $model) {
    return $model->enabled == 1;
}, 'None'); // obviously an awful example in terms of what it returns

```

- New `array_last` helper (side-effect of some other work which was reverted) that works the same as array_first ([`88421dd`](88421ddc6b2313ec16cd65b66a03ee8258e02702))

- `Collection#sortBy` now takes [sort flags](http://www.php.net/manual/en/function.sort.php) as its second parameter ([`426c9e0`](426c9e0e39915eedd2861b147ba6701443136e3d))

- You can now retrieve failed recipients to `Mail::send()` with `Mail::failures()` ([a0086a5](a0086a5779c21a06def56d9cff9bb51a3ed3f242))