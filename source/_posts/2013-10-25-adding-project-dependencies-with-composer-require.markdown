---
layout: post
categories: [Code, PHP, Composer]
date: 2013-10-25 20:22
comments: false
title: "Adding Project Dependencies with composer require"
---

Composer's been around for some time now, and gained a lot of popularity since its use in Laravel 4. I noticed back in the first half of 2013 that a lot of blogs and tutorials were teaching installation of packages (or, more correctly, addition of project dependencies) by editing your `composer.json` file's `require` array and then doing a `composer update` on the command line. I thought back then that maybe because composer was new to these people (in general these were related to Laravel 4 which was in development at the time) they hadn't yet discovered the easier, and technically less error-prone, way to do this that very few people seemed to be using and advising: `composer require`

Well it seems that most people still haven't discovered the command, so I figured I should write about it. Instead of erroneously manually editing your `composer.json` file to add a new package to your project's dependencies and then calling `composer update`, you can use `composer require` to do the job for you. You're having to use the command line anyway, so why not skip the manual editing of `composer.json` part?

The syntax is really simple:

    composer require vendor/package version

It's as easy as that. And you can even require a dev dependency with the `--dev` option:

    composer require mockery/mockery 0.8.0 --dev

In fact, the command can be used to add multiple packages to your project's dependencies by simply separating them with a space. In this case, it's probably best to use the alternative version constraint syntax which is `vendor/package:version` or `vendor/package=version`:

    composer require --dev mockery/mockery:0.8.0 phpunit/phpunit:~3.7.4

Also notice how the `--dev` can go either before or after the packages -- use what works best for you.

---

There is one caveat to this: if you wish to use the asterisk to denote any patch or minor version is okay, then it's best to put the argument in question in quotes so that your shell doesn't try (and probably fail) to interpret it as a glob on the filesystem. In my case (using [fish](http://fishshell.com/)) the following happens:

    > composer require mockery/mockery 0.8.* --dev
    fish: No matches for wildcard '0.8.*'.
    composer require mockery/mockery 0.8.* --dev
                                     ^

However, with the argument quoted it's all good:

    composer require "mockery/mockery 0.8.*" --dev