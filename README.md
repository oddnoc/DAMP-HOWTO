# DAMP

* Darwin (Mac OS X)
* Apache
* MySQL
* PHP

## Ports

Install the following ports with sudo port install *portname*.
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

	sudo mkdir -p /opt/local/var/db/mysql5
	sudo chown _mysql /opt/local/var/db/mysql5
	sudo mysql_install_db5 # don't set the user as recommended

## Then unload mysql and CHOWN again as above:

	sudo port unload mysql5-server
	sudo chown _mysql /opt/local/var/db/mysql5
	sudo port load mysql5-server

## phpmyadmin setup

	cd /opt/local/apache2/htdocs
	sudo ln -s /opt/local/www/phpmyadmin
	bbedit /opt/local/www/phpmyadmin/config.inc.php # add a blowfish passphrase (can be anything, like glory4meterage)

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


## log files [clear these occasionally; cronolog makes it easier to delete oldies]

* /opt/local/apache2/logs
