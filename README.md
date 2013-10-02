# DAMP for Mac OS X Mountain Lion through Homebrew

## What does DAMP stand for?

* Darwin (the open-source part of Mac OS X)
* Apache
* MySQL
* PHP

## Result of executing these instructions

You will end up with all the services you need on your Mac to run PHP
applications. Any directory you create under your Sites directory will
correspond to a site: http://<var>dirname</var>.lh.fredcondo.net/. (Note that
this relies on my wildcard DNS address record such that *.lh.fredcondo.net
resolves to 127.0.0.1.) In addition, you will have PHPMyAdmin at
<http://localhost/phpmyadmin>.

**Note:** homebrew does not use `sudo`. You may be accustomed to using it all
the time on your Mac, but in the following instructions, use `sudo` *only when
it is explicitly mentioned.*

**Note:** These instructions were developed on and have been tested on Mountain
Lion *only.*

## Homebrew (package manager)

Get and install the [Homebrew][brew] package manager.

	ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"

Keep your homebrew up to date by regularly doing `brew update`.

Add bash completion for brew to your .profile:

	source `brew --prefix`/etc/bash_completion

If you decide to install the homebrew bash, make it your default shell:

	chsh -s `brew --prefix`/bin/bash

## Ports

Install the following ports with `brew install` *formula*.

PHP is maintained separately from the main homebrew formulae. To gain access to
the [PHP formulae][github], tap into the PHP repository:

	brew tap homebrew/dupes
	brew tap josegonzalez/homebrew-php

* cronolog
* php53 `--with-mysql --with-tidy --with-intl --with-homebrew-openssl` _(lengthy build)_
* curl-ca-bundle
* php53-intl
* mysql _(follow the on-screen directions to launch the server)_
* php53-apc
* php53-mcrypt _(for phpmyadmin)_
* phpmyadmin

## Enable apache

Create your configuration file:

	sudo touch /etc/apache2/users/`whoami`.conf
	sudo chown `whoami` /etc/apache2/users/`whoami`.conf

Edit the file so that it reads as follows, but substitute your username (the
output of `whoami`) for "UID":

	LoadModule php5_module    /usr/local/opt/php53/libexec/apache2/libphp5.so
	LogFormat "%V %h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combinedvhost
	User UID
	Group UID
	<Directory "/Users/UID/Sites/">
		 Options Indexes MultiViews FollowSymLinks
		 AllowOverride All
		 Order allow,deny
		 Allow from all
	</Directory>
	<VirtualHost *.lh.fredcondo.net:80>
		servername *
		UseCanonicalName off
		VirtualDocumentRoot /Users/UID/Sites/%1/
		addDefaultcharset on
		DirectoryIndex index.php
		CustomLog "|/usr/local/sbin/cronolog -H /Users/UID/log/access_log /Users/UID/log/%Y/%m/%d/access_log" combinedvhost
		ErrorLog "|/usr/local/sbin/cronolog -H /Users/UID/log/error_log /Users/UID/log/%Y/%m/%d/error_log"
	</VirtualHost>

	Alias /phpmyadmin /usr/local/share/phpmyadmin
	<Directory /usr/local/share/phpmyadmin/>
		Options Indexes FollowSymLinks MultiViews
		AllowOverride All
		Order allow,deny
		Allow from all
	</Directory>

**Note:** you might not have a personal group. You can check this with the
`groups` shell command. If that's the case, substitute one of your other groups,
such as admin, in the `Group` directive above.

Make sure you have a log directory:

	mkdir ~/log

Then set apache to launch at boot:

	sudo defaults write /System/Library/LaunchDaemons/org.apache.httpd Disabled -bool false

When you need to start/stop/restart apache, use apachectl with sudo:

	sudo apachectl start
	sudo apachectl stop
	sudo apachectl restart

## php.ini

To customize php, edit php.ini (you can discover where php.ini is by doing `php --ini`
on the command line). You'll definitely need to set a time zone. Search for
date.timezone and add a timezone code (example: *America/Los_Angeles*). If you
want a separate configuration for the command line (CLI) php, copy php.ini to
php-cli.ini and edit the latter.

## Add CLI PHP to your path

Add the php CLI command to your path in your `.profile` with this:

	PATH="$(brew --prefix josegonzalez/php/php53)/bin:$PATH"

## Version
* 1.1.1 Add notes about Mountain Lion and personal group
* 1.1.0 Corrections & expanded explanations
* 1.0.1 Minor fixes
* 1.0.0 Switched from MacPorts to homebrew.
* 0.2.2 Fixed typo in previous version number. Improved mysql setup.
* 0.2.1 Added php.ini section.
* 0.2.0 Added many details and fixes.
* 0.1.1  Replaced reference to 'bbedit' with '$EDITOR'
* 0.1 Added TLS support and formatted README as markdown.

[brew]: http://brew.sh/
[github]: https://github.com/josegonzalez/homebrew-php
