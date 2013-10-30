OS-X-Dev-Environment
====================

Notes on creating an OS X native development environment. My primary
goals were to be able to do Drupal 8 development with Xdebug support,
which meant that MAMP wasn't a viable option. I also wanted to do
work in Ruby and make another shot at getting an Xcode environment
for Arudino development going. A secondary goal was to have something
easier to understand than Mark Sonnabaum's Megalodon. Megalodon is
where I'd like to end up, but when it breaks there is a lot to sort
out. I was hoping for something with less of an initial learning
curve.

Drupal Dev Environment Setup Notes
----------------------------------

Useful advice/notes from:

* Karen Stevenson: http://www.lullabot.com/blog/article/setting-my-mac-without-mamp
* Niepi's (aka Thomas Niepraschk): https://gist.github.com/niepi/1932534
* Angus Mak: http://www.lullabot.com/blog/article/configuring-xdebug-osx-mountain-lion

Stock Mavericks, plus:

Commercial Tools
----------------
* Xcode command line tools (or full Xcode)
* Sublime Text
* PHPstorm
  * Templates for Drupal: https://drupal.org/project/phpstorm-templates
* Sequel Pro (MariaDB/MySQL management tool)
* Codebug - didn't install (http://www.codebugapp.com) but it might be interesting to check out.

Homebrew
--------

```
ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

edconf

Git
---
Installing Xcode gets you git. For later versions of git install from Homebrew

```
brew install git
```

Nice additions to git:
* Command & branch autocompletion: https://github.com/git/git/blob/master/contrib/completion/git-completion.bash

Once you've got Homebrew installed:

```
brew install bash-completion
```

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
