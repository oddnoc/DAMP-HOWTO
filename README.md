# DAMP for Mac OS X Mountain Lion or Mavericks through Homebrew

**NOTE:** This document is historical, and is not being actively maintained, because I have been using [nginx][nginx] on my Mac and in production for some time now.

## What does DAMP stand for?

* Darwin (the open-source part of Mac OS X)
* Apache
* MySQL
* PHP

## Result of executing these instructions

You will end up with all the services you need on your Mac to run PHP applications. Any directory you create under your Sites directory will correspond to a site: http://<var>dirname</var>.lh.fredcondo.net/. (Note that this relies on my wildcard DNS address record such that *.lh.fredcondo.net resolves to 127.0.0.1.) In addition, you will have PHPMyAdmin at <http://localhost/phpmyadmin>.

**Note:** homebrew does not use `sudo`. You may be accustomed to using it all the time on your Mac, but in the following instructions, use `sudo` *only when it is explicitly mentioned.*

**Note:** These instructions were developed on and have been tested on Mountain Lion with PHP 5.3, and everything still worked after upgrading to Mavericks and heeding the [upgrade advice][mavad] from the Homebrew-PHP project, then switching to PHP 5.4.

## Homebrew (package manager)

Get and install the [Homebrew][brew] package manager.

```shell
ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

Keep your homebrew up to date by regularly doing `brew update`.

Add bash completion for brew to your .profile:

```shell
source `brew --prefix`/etc/bash_completion
```

If you decide to install the homebrew bash, make it your default shell:

```shell
chsh -s `brew --prefix`/bin/bash
```

## Ports

Install the following ports with `brew install` *formula*.

PHP is maintained separately from the main homebrew formulae. To gain access to the [PHP formulae][github], tap into the PHP repository:

```shell
brew tap homebrew/dupes
brew tap josegonzalez/homebrew-php
```

* cronolog
* curl-ca-bundle
* php54 `--with-mysql --with-homebrew-openssl` _(lengthy build)_
* php54-intl
* php54-opcache
* php54-mcrypt _(for phpmyadmin)_
* php54-xdebug
* mysql _(follow the on-screen directions to launch the server)_
* phpmyadmin

**Note:** wherever php54 is mentioned, you can substitute the version you want, such as php55. You can install multiple versions if you follow [the directions][phpmult].

## Enable apache

Create your configuration file:

```shell
sudo touch /etc/apache2/users/`whoami`.conf
sudo chown `whoami` /etc/apache2/users/`whoami`.conf
```

Edit the file so that it reads as follows, but substitute your username (the output of `whoami`) for "UID" (and you'll have to adjust the "5.4.22" to reflect the actual version of PHP):

```apacheconf
LoadModule php5_module    /usr/local/Cellar/php54/5.4.22/libexec/apache2/libphp5.so
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
```

**Note:** you might not have a personal group. You can check this with the `groups` shell command. If that's the case, substitute one of your other groups, such as admin, in the `Group` directive above.

Make sure you have a log directory:

```shell
mkdir ~/log
```
Then set apache to launch at boot:

```shell
sudo defaults write /System/Library/LaunchDaemons/org.apache.httpd Disabled -bool false
```

When you need to start/stop/restart apache, use apachectl with sudo:

```shell
sudo apachectl start
sudo apachectl stop
sudo apachectl restart
```

## php.ini

To customize php, edit php.ini (you can discover where php.ini is by doing `php --ini` on the command line). You'll definitely need to set a time zone. Search for date.timezone and add a timezone code (example: *America/Los_Angeles*). If you want a separate configuration for the command line (CLI) php, copy php.ini to php-cli.ini and edit the latter.

## Add CLI PHP to your path

Add the php CLI command to your path in your `.profile` with this:

```shell
PATH="$(brew --prefix josegonzalez/php/php54)/bin:$PATH"
```

## Version
* 1.2.1 Add historical notice and license
* 1.2.0 Mavericks & PHP 5.4
* 1.1.2 Correct php.ini location
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
[mavad]: https://github.com/josegonzalez/homebrew-php#common-upgrade-issues
[phpmult]: https://github.com/josegonzalez/homebrew-php/#installing-multiple-versions
[nginx]: http://nginx.org

## License

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>

THIS DOCUMENT IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS DOCUMENT INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS DOCUMENT.
