# DAMP

* Darwin (Mac OS X)
* Apache
* MySQL
* PHP

## MacPorts

Get and install the [MacPorts](http://www.macports.org/) distribution appropriate for your version of Mac OS X.

* [MacPorts for Snow Leopard (10.6)](http://distfiles.macports.org/MacPorts/MacPorts-1.9.2-10.6-SnowLeopard.dmg)
* [MacPorts for Leopard (10.5)](http://distfiles.macports.org/MacPorts/MacPorts-1.9.2-10.5-Leopard.dmg)
* [MacPorts for Tiger (10.4)](http://distfiles.macports.org/MacPorts/MacPorts-1.9.1-10.4-Tiger.dmg)

Keep your MacPorts up to date by occasionally doing `sudo port -v selfupdate`.

## Ports

Install the following ports with `sudo port install` *portname*.
For php5 itself, the command is:

	sudo port install php5 +apache2 +pear

*	cronolog
*	php5 +apache2 +pear
*	php5-apc
*	php5-gd
*	php5-iconv
*	php5-mbstring
*	php5-mcrypt (for phpmyadmin)
*	php5-posix
*	php5-tidy
*	php5-mysql
*	phpmyadmin
*	mysql5-server

## one-time mysql setup

	sudo mysql_install_db5
	sudo chown -R _mysql /opt/local/var/db/mysql5
	sudo port load mysql5-server
	sudo mysql_secure_installation5

## phpmyadmin setup

	cd /opt/local/apache2/htdocs
	sudo ln -s /opt/local/www/phpmyadmin
	$EDITOR /opt/local/www/phpmyadmin/config.inc.php # add a blowfish passphrase (can be anything, like glory4meterage)

## Apache/mysql restart

	sudo port unload apache2 && sudo port load apache2
	sudo port unload mysql5-server && sudo port load mysql5-server

## Apache config

* /opt/local/apache2/conf
* see diff files
* create this file as extra/mod_php.conf

		<IfModule mod_php5.c>
		AddType  application/x-httpd-php         .php
		AddType  application/x-httpd-php-source  .phps
		</IfModule>


## log files

Clear or delete these occasionally; cronolog makes it easier to delete oldies.

* /opt/local/apache2/logs

## Version
* 0.1 Added TLS support and formatted README as markdown.
* 0.1.1  Replaced reference to 'bbedit' with '$EDITOR'
