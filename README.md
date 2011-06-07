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

## one-time mysql setup

	sudo mysql_install_db5
	sudo chown -R _mysql /opt/local/var/db/mysql5
	sudo port load mysql5-server
	sudo mysql_secure_installation5

## phpmyadmin setup

1. create a symlink so you can access phpmyadmin

		cd /opt/local/apache2/htdocs
		sudo ln -s /opt/local/www/phpmyadmin
1. Set a blowfish passphrase so that cookies work (can be anything, like glory4meterage)

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

## SSL/TLS key & certificate

1. Generate a self-signed key & certificate

		openssl genrsa 2048 > server.key
		openssl req -new -x509 -nodes -sha1 -days 3650 -key server.key > server.crt

1. Make them read-only and owned by root

		chmod 400 server.key server.crt
		sudo chown root server.key server.crt

1. Move them to /opt/local/apache2/conf

		sudo mv server.key server.crt /opt/local/apache2/conf

## Apache first start

	sudo port load apache2

## Apache/mysql restart

	sudo port unload apache2 && sudo port load apache2
	sudo port unload mysql5-server && sudo port load mysql5-server


## log files

Clear or delete these occasionally; cronolog makes it easier to delete oldies.

* /opt/local/apache2/logs

## Version
* 0.1 Added TLS support and formatted README as markdown.
* 0.1.1  Replaced reference to 'bbedit' with '$EDITOR'
