---
title: PHP
weight: 60
tags:
  - php
  - languages
category: Languages &amp; Frameworks
redirect_from:
  - /languages/php/
---
* include a table of contents
{:toc}

## Versions
We use **PHPENV** to manage PHP versions. We currently have **5.3**, **5.4**, **5.5**, **5.6**, **7.0** and **7.1** installed.
These are aliases that are pointing to the specific 5.x.x and 7.x.x versions we have installed.

For a full list of installed versions (including patch level versions) open a SSH debug build and run the following command

```shell
phpenv versions
```

### Ubuntu 14.04
By default we use 5.5

Specific versions: 5.3.29, 5.4.45, 5.5.38, 5.6.29, 7.0.14 and 7.1.0

***Do not rely on the specific versions we have as this can change any time and could break your build.***

You can change the version you want to use by running `phpenv local PHP_VERSION` in your setup commands.
For example

```shell
phpenv local 5.5
```

## PHP.ini

You can access and change the php.ini file in

```shell
/home/rof/.phpenv/versions/$(phpenv version-name)/etc/php.ini
```

## Dependencies and Extensions

You can install dependencies through pear and composer. Extensions can be installed through pecl. If you need any other tools please send us a message.

### xdebug

By default `xdebug` is installed and enabled on the build VMs. If you want to remove it, add the following command to your builds.

```shell
rm -f /home/rof/.phpenv/versions/$(phpenv version-name)/etc/conf.d/xdebug.ini
```

### Pear

```shell
pear install pear/PHP_CodeSniffer
```

### Composer

```shell
composer install --no-interaction
```

#### Cache

If you want cache `composer` dependencies during builds, please add the following environment variable to your build configuration.

```shell
COMPOSER_HOME=${HOME}/cache/composer
```

To make sure that the dependency cache is used by all of your dependencies, please call `composer` via the following snippet.

```
composer install --prefer-dist  --no-interaction
```

#### GitHub API

To speed up Composer you can install packages from `dist` by replacing `--prefer-source` with `--prefer-dist`. However as GitHub's API is rate limited, you then might see errors similar to this:

```shell
- Installing phpunit/phpunit (3.7.37)
Downloading: connection... Failed to download phpunit/phpunit from dist: Could not authenticate against github.com`
Now trying to download from source
```

To avoid this create a [new personal access token on GitHub](https://github.com/settings/tokens/new). For the description you can use something like _Codeship Composer_ and you can unselect all scopes. Copy your personal access token and add it to the environment variables in your Codeship project settings (on the _Environment_ page) as `GITHUB_ACCESS_TOKEN`.

Then use the following Setup Commands in your Codeship project settings:

```shell
composer config -g github-oauth.github.com $GITHUB_ACCESS_TOKEN
composer install --prefer-dist --no-interaction
```

### PHPUnit

As we already have a version of PHPUnit preinstalled on our build VMs, you need to remove this version first, before upgrading to a different version via Composer.

```shell
composer global remove phpunit/phpunit
composer global require phpunit/phpunit:5.*
```

### Pecl

```shell
pecl install -f memcache
```

## phpenv rehashing
If you install a new executable through a pear package make sure to run

```shell
phpenv rehash
```

so phpenv is aware of the new executable and sets it up correctly

## Running your PHP Server

If you want to test against a running PHP Server you can use the builtin one to
start a server in the current directory. It will serve files from this directory.

```shell
nohup bash -c "php -S 127.0.0.1:8000 2>&1 &" && sleep 1; cat nohup.out
```

You can access it in your tests on 127.0.0.1:8000.

Also take a look at the PHP built-in webserver docs in the
[PHP documentation](http://www.php.net/manual/en/features.commandline.webserver.php)

Thanks to [Jeff Donios](https://github.com/doniosjm) for the tip.
