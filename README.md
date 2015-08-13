OS-X-Dev-Environment
====================

Notes on creating an OS X native development environment. My primary goals are to be able to do Drupal 8 development with Xdebug support, which meant that MAMP wasn't a viable option. I also wanted to do Meteor development (both in native JavaScript and using TypeScript), work in Ruby, and make another shot at getting an Xcode environment for Arudino development going.

A secondary goal was to have something easier to understand than Mark Sonnabaum's [Megalodon](https://github.com/msonnabaum/megalodon). Megalodon, or something like it, is where I'd like to end up, but when it breaks there is a lot to sort out. I was hoping for something with less of an initial learning curve.

Since I'm now teaching computer science (ECS and IB Computer Science) I want to keep the development tools fairly close to the stock OS X tools (just for the ease of lab managment and the buy in of the district computer support folks).

Drupal Dev Environment Setup Notes
----------------------------------

Useful advice/notes from:

* Alan Ivey: [OS X 10.10 Yosemite Local Development Environment: Apache, PHP, and MySQL with Homebrew](https://echo.co/blog/os-x-1010-yosemite-local-development-environment-apache-php-and-mysql-homebrew)
*

Basically, I followed this verbatim – installing on 10.11 PB4, the only problems I ran into were:

* Xcode 7-beta needed to be installed and command line tools downloaded.
* PHP 5.6 failed to compile, the solution required [modifying a makefile as described by alsantos123](https://github.com/Homebrew/homebrew-php/issues/1941).

Previous versions also learned from:

* Karen Stevenson: http://www.lullabot.com/blog/article/setting-my-mac-without-mamp
* Niepi's (aka Thomas Niepraschk): https://gist.github.com/niepi/1932534
* Angus Mak: http://www.lullabot.com/blog/article/configuring-xdebug-osx-mountain-lion

My starting point was the latest El Capitan public beta (10.11.4 as I write this) plus:

Commercial Tools
----------------
* Xcode (if you don't need the Xcode IDE you could also just install the command line tools), I used the stock version from the App Store – the beta of Xcode 7 was out, but I decided not to live on the bleeding edge…

  To use Xcode, and the command line tools which Homebrew depends on, you have to agree to the license. You can either launch Xcode or run this command:

  ```
  sudo xcodebuild -license
  ```

* [Atom](https://atom.io)
  * TypeScript
* [PHPstorm](https://www.jetbrains.com/phpstorm)
  * [Templates for Drupal](https://drupal.org/project/phpstorm-templates)
* [Sequel Pro](http://www.sequelpro.com) (MariaDB/MySQL management tool)
* [Codebug](http://www.codebugapp.com) - haven't used it, but it looks like it might be interesting.

Homebrew
--------

```
ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

Editor Config
-------------

Provides an easy way to maintain common coding standards across multiple editors (Atom, Emacs, PHPStorm, vi, Xcode, etc.).

http://editorconfig.org

Git
---
Installing Xcode gets you git. Yosemite comes with 2.4.4 as of 10.10.4. For later versions of git install from Homebrew:

```
brew install git
```

Nice additions to git:
* Command & branch autocompletion: https://github.com/git/git/blob/master/contrib/completion/git-completion.bash

Once you've got Homebrew installed:

```
brew install bash-completion
```

Then add the following lines to your .bash_profile

```
if [ -f $(brew --prefix)/etc/bash_completion ]; then
   . $(brew --prefix)/etc/bash_completion
fi
```

The Homebrew formula also reminds you to do this.

PHP
---
* Use stock version, you will need to recompile if you want to use PostgreSQL,
Homebrew will make this easy.
* Copy sample config file /etc/php.ini.default to /etc/php.ini
* Xdebug configuration is below

Installing a newer version of PHP requires setting up a "tap" in Homebrew.
Homebrew, by default, doesn't support packages that are distributed with
Mac OS X. Many online notes assume that this is already done and you'll find
that Homebrew doesn't work on things that other people clearly think will
work. So do this first (if you have't already):

```
brew tap homebrew/dupes
brew tap josegonzalez/php
```

Now the PHP formulae will work.

Apache
------
* Use stock version
* Enable PHP module
* Enable clean URLs
* Enable virtual hosts
* Create working virtual host entries (DocumentRoot is wrong in examples).

MariaDB
-------
* The distributed PHP is built with MariaDB/MySQL support.
* To compile, the gcc-42 package is needed, if necessary it can be installed from Homebrew.

```
brew install mariadb
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mariadb.plist
```

Test the database connection with Sequel Pro or

```
mysql -uroot
```

PostgreSQL
----------
* If Server.app is installed there is a native instance of PostgreSQL. If it is not, the PostgreSQL can be installed with Homebrew.
* These instructions (untested) seems promising: https://gist.github.com/lxneng/741932
* The distributed PHP doesn't appear to support PostgreSQL. So PHP will need to be rebuilt. Homebrew has the necessary formulae (once the PHP tap is added – see above).
* Other options for installing PostgreSQL can be found here: http://www.postgresql.org/download/macosx

```
brew install postgresql
```

Xdebug (http://xdebug.org)
------
* Use distributed version. Mavericks ships with the 2.2.3.
* Base setup on: http://www.fieg.nl/installing-xdebug-on-mac-osx, http://www.lullabot.com/blog/article/configuring-xdebug-osx-mountain-lion,  and http://stackoverflow.com/questions/17215729/xdebug-install-php-ini-os-x-10-8-mountain-lion

```
diff --git i/etc/php.ini w/etc/php.ini
index 814455b..7068313 100644
--- i/etc/php.ini
+++ w/etc/php.ini
@@ -905,10 +905,19 @@ default_socket_timeout = 60
 ;extension=php_xsl.dll
 ;extension=php_zip.dll

+; Start off using the distributed version of Xdebug.
+zend_extension=/usr/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so
+
 ;;;;;;;;;;;;;;;;;;;
 ; Module Settings ;
 ;;;;;;;;;;;;;;;;;;;

+[xdebug]
+xdebug.remote_enable=1
+xdebug.remote_host=localhost
+xdebug.remote_port=9000
+xdebug.remote_handler="dbgp"
+
 [CLI Server]
 ; Whether the CLI web server uses ANSI color coding in its terminal output.
 cli_server.color = On
```

Drush (https://github.com/drush-ops/drush)
-----
* Use latest for D8 support.
* Useful stuff on Mark Sonnabaum's GitHub page: https://github.com/msonnabaum/drush. Good configuration and troubleshooting tips there.
* And here: http://nuvole.org/blog/2010/nov/09/typing-less-drush-aliases-and-autocompletion.
* Add drush completion to your shell.

```
git clone --branch master https://github.com/drush-ops/drush.git
ln -s $(pwd)/drush/drush /usr/local/bin/ # Or ~/bin/drush
```

Dreditor (http://dreditor.org)
--------
* Download from project page.

Ruby/Volt
---------

```shell
brew install ruby # OSX ships with a version, but it is a but old.
gem install volt
volt new PROJECTNAME
```
