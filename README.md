macports-lamp
=============

Builds a LAMP (MAMP) stack on macOS 10.13 High Sierra using MacPorts.

Intended to be used on a fresh 10.13 install. For previous versions of macOS
use the pre-high-sierra branch.

Please read this README all the way through before beginning.

Why
---
MacPorts is powerful but finicky. I commonly end up with MySQL/MariaDB with
the wrong permissions and other annoyances. Therefore this script, which will
reliably install MariaDB 5.5 and PHP 5.6 plus Apache 2.4 on OS X. At least,
for me. Tested with MacPorts 2.4.2 on OS X 10.13.

Prerequisites
-------------
If you had installed macports-lamp previously, export any MySQL databases
(since macports-lamp will install a clean 'n' fresh MariaDB).

Stop any running MacPorts services, e.g.,

```
sudo port unload apache2
sudo port unload mysql55-server
```

Move the old MacPorts installation out the way.

```
sudo mv /opt/local /opt/local.old
```

Install [MacPorts](http://www.macports.org/install.php) with its prerequisites.

Make sure to install XCode and do

```
sudo xcodebuild -license
xcode-select --install
```

macports-lamp comes with a my.cnf file for MariaDB that it will place at
/etc/my.cnf. If you already had a my.cnf there it will be moved out of the way.

MariaDB will use the following socket:

```
socket		= /opt/local/var/run/mariadb/mysqld.sock
```

Read Through the Script
-----------------------

You will be prompted for the root password that you want to use with MariaDB.

Default Virtual Host
--------------------

The script will create the following virtual host which can be reached by
going to http://local.test/ after the script completes.

```
<VirtualHost *:80>
    ServerName local.test
    DocumentRoot /Users/username/Sites
    <Directory /Users/username/Sites>
      Options Indexes FollowSymLinks
      DirectoryIndex index.php index.html
      Require local
      AllowOverride All
    </Directory>
</VirtualHost>
```

Shortcuts
---------

The following functions will be installed in ~/.bash_profile. I find them useful
at the command line:

```
te - tail Apache error log
ta - tail Apache access log
tp - tail PHP error log
acr - apachectl restart (really sudo port unload apache2;sudo port load apache2)
```

Running the Scripts
-------------------

```
sudo ./build_lamp
sudo ./build_mysql
```

One of the PHP 7 ports has a dependency on Python; thus MacPorts will install
Python 2.7. If you would like to make this the default version of python that
runs when you run python scripts (recommended), tell MacPorts that with

```
sudo port select --set python python27
```

Switching between PHP 5.6 and PHP 7.0:

Switch to PHP 5.6 (default after installation):
```
sudo port select php php56
sudo /opt/local/bin/apxs -A -e -n php7 mod_php70.so
sudo /opt/local/bin/apxs -a -e -n php5 mod_php56.so
acr
```

Switch to PHP 7.0:
```sudo port select php php70
sudo /opt/local/bin/apxs -A -e -n php5 mod_php56.so
sudo /opt/local/bin/apxs -a -e -n php7 mod_php70.so
acr

```

Drush
-----

To install Drush, see http://docs.drush.org/en/master/install/

Reference
---------
https://trac.macports.org/wiki/howto/MAMP

http://aaronbonner.io/post/44973182283/a-guide-to-php-mysql-and-nginx-on-macports

