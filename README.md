# DAMP for Mac OS X through MacPorts

## What does DAMP stand for?

* Darwin (the open-source part of Mac OS X)
* Apache
* MySQL
* PHP

## Result of executing these instructions

You will end up with all the services you need on your Mac to run PHP
applications. Any directory you create under your Sites directory will
correspond to a site: http://_dirname_.lh.fredcondo.net/. In addition, you'll
have PHPMyAdmin at [http://localhost/phpmyadmin](http://localhost/phpmyadmin).

## MacPorts

Get and install the [MacPorts](http://www.macports.org/) distribution appropriate for your version of Mac OS X.

* [MacPorts for Mountain Lion (10.8)](https://distfiles.macports.org/MacPorts/MacPorts-2.1.2-10.8-MountainLion.pkg)
* [MacPorts for Lion (10.7)](https://distfiles.macports.org/MacPorts/MacPorts-2.1.2-10.7-Lion.pkg)
* [MacPorts for Snow Leopard (10.6)](http://distfiles.macports.org/MacPorts/MacPorts-1.9.2-10.6-SnowLeopard.dmg)
* [MacPorts for Leopard (10.5)](http://distfiles.macports.org/MacPorts/MacPorts-1.9.2-10.5-Leopard.dmg)
* [MacPorts for Tiger (10.4)](http://distfiles.macports.org/MacPorts/MacPorts-1.9.1-10.4-Tiger.dmg)

Keep your MacPorts up to date by occasionally doing `sudo port -v selfupdate`.

## Ports

Install the following ports with `sudo port install` *portname*.
For php5 itself, the command is:

	sudo port install php5 +apache2 +pear

*	cronolog
*	php5 +apache2 +pear _(lengthy build)_
*	php5-apc
*	php5-gd
*	php5-iconv
*	php5-mbstring
*	php5-mcrypt _(for phpmyadmin)_
*	php5-posix
*	php5-tidy
*	php5-mysql
*	phpmyadmin
*	mysql5-server _(lengthy build)_

Here it is as a one-line command:

	sudo port install php5 +apache2 +pear && \
	sudo port install cronolog php5-apc php5-gd php5-iconv php5-mbstring php5-mcrypt php5-posix php5-tidy php5-mysql phpmyadmin mysql5-server


## one-time mysql setup

	sudo chown -R _mysql /opt/local/var/db/mysql5
	sudo -u _mysql mysql_install_db5
	sudo port load mysql5-server
	sudo -u _mysql mysql_secure_installation5

## phpmyadmin setup

1. create a symlink so you can access phpmyadmin

		cd /opt/local/apache2/htdocs
		sudo ln -s /opt/local/www/phpmyadmin
1. Set a blowfish passphrase so that cookies work (can be anything, like glory4meterage).
   You may also need to change "localhost" to "127.0.0.1"

		sudo $EDITOR /opt/local/www/phpmyadmin/config.inc.php

## Apache config

1. `sudo rm /opt/local/apache2/htdocs/index.html`
2. `cd /opt/local/apache2/conf`
3. see diff files, which you can apply with `patch -p0` _diff-file-name_
3. Edit httpd.conf, near line 129, to apply your username and group (suggestion: _staff_)
4. create this file as extra/mod_php.conf

		<IfModule mod_php5.c>
		AddType  application/x-httpd-php         .php
		AddType  application/x-httpd-php-source  .phps
		</IfModule>
5. _(optional)_ Edit extra/httpd-vhosts.conf (near line 47) to use your own domain.
6. If you get an error on startup about mod\_unique\_id, disable its Load line in httpd.conf

## SSL/TLS key & certificate

1. Generate a self-signed key & certificate

		openssl genrsa 2048 > server.key
		openssl req -new -x509 -nodes -sha1 -days 3650 -key server.key > server.crt

1. Make them read-only and owned by root

		chmod 400 server.key server.crt
		sudo chown root server.key server.crt

1. Move them to /opt/local/apache2/conf

		sudo mv server.key server.crt /opt/local/apache2/conf

## php.ini

To customize php, copy /opt/local/etc/php5/php.ini-development to
/opt/local/etc/php5/php.ini and then make changes. You'll definitely need to
set a time zone. Search for date.timezone and add a timezone code (example:
*America/Los_Angeles*)

You may also want to do this:

	To use mysqlnd with a local MySQL server, edit /opt/local/etc/php5/php.ini and set
	mysql.default_socket, mysqli.default_socket and pdo_mysql.default_socket
	to /opt/local/var/run/mysql5/mysqld.sock

## Apache first start

	sudo port load apache2

## Apache/mysql restart

	sudo port unload apache2 && sudo port load apache2
	sudo port unload mysql5-server && sudo port load mysql5-server


## log files

Clear or delete these occasionally; cronolog makes it easier to delete oldies.

* /opt/local/apache2/logs

## Version
* 0.2.2 Fixed typo in previous version number.
* 0.2.1 Added php.ini section.
* 0.2.0 Added many details and fixes.
* 0.1.1  Replaced reference to 'bbedit' with '$EDITOR'
* 0.1 Added TLS support and formatted README as markdown.
