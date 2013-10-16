---
layout: post
categories: [Laravel, Laravel Digest, Code]
date: 2013-11-01 13:20
comments: false
title: "Laravel Digest (October 2013)"
---

Welcome to the second installment of Laravel Digest, the series where I give a regular rundown of important changes, fixes and additions to Laravel's master branch. Below are the changes up to the end of October 2013

- Blade braces can now be escaped using `@` which is useful for people using clientside templating languages that also use the braces (`cb93bf3`):

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
