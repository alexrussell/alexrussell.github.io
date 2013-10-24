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

- Router input method? - TODO

- x