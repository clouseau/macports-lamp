macports-lamp
=============

Builds a LAMP (MAMP) stack on macOS 10.15 Catalina using MacPorts.

Intended to be used on a fresh 10.15 install. 

The main branch supports MySQL 5.7.

The mariadb55 branch supports MariaDB 5.5.

For versions of macOS before 10.13 use the pre-high-sierra branch.

Please read this README all the way through before beginning.

Why
---
MacPorts is powerful but finicky. I commonly end up with MySQL with
the wrong permissions and other annoyances. Therefore this script, which will
reliably install MariaDB 5.7 and PHP 5.6 and 7.2/7.3 plus Apache 2.4 on OS X. 
At least, for me. Tested with MacPorts 2.6.4 on OS X 10.15.

Prerequisites
-------------
If you had installed macports-lamp previously, export any MySQL databases
(since macports-lamp will install a clean 'n' fresh MySQL).

Stop any running MacPorts services as in the following two examples. If 
these give you errors, just continue.

```
sudo port unload apache2
sudo port unload mariadb-server
```

Move/rename /Applications/MacPorts.

Move the old MacPorts installation in /opt out of the way. If you have System Integrity Protection enabled, you will need to [disable it first](https://developer.apple.com/documentation/security/disabling_and_enabling_system_integrity_protection).

```
sudo mv /opt/local /opt/local.old
```

Install [MacPorts](http://www.macports.org/install.php) with its prerequisites.

Make sure to install XCode and do

```
sudo xcodebuild -license
xcode-select --install
```

macports-lamp comes with a my.cnf file for MySQL 5.7 that it will place at
/etc/my.cnf. If you already had a my.cnf there it will be moved out of the way.

MySQL 5.7 will use the following socket:

```
socket		= /opt/local/var/run/mysql57/mysqld.sock
```

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
sudo ./increase_limits
```

One of the PHP 7 ports has a dependency on Python; thus MacPorts will install
Python 2.7. If you would like to make this the default version of python that
runs when you run python scripts (recommended), tell MacPorts that with

```
sudo port select --set python python27
```

Switching between PHP 5.6 and PHP 7.3:

Switch to PHP 5.6:
```
sudo port select php php56
sudo /opt/local/bin/apxs -A -e -n php7 mod_php72.so
sudo /opt/local/bin/apxs -A -e -n php7 mod_php73.so
sudo /opt/local/bin/apxs -a -e -n php5 mod_php56.so
acr
```

Switch to PHP 7.3:
```
sudo port select php php73
sudo /opt/local/bin/apxs -A -e -n php5 mod_php56.so
sudo /opt/local/bin/apxs -A -e -n php7 mod_php72.so
sudo /opt/local/bin/apxs -a -e -n php7 mod_php73.so
acr

```

If you disabled System Integrity Protection earlier, don't forget to [reenable it](https://developer.apple.com/documentation/security/disabling_and_enabling_system_integrity_protection). You can check if it is enabled by typing `csrutil status` in Terminal.

Troubleshooting
---------------

If the MySQL installation messed up somehow, and you want to try it again, use the following to clean up and retry:

```
sudo port unload mysql57-server
sudo port uninstall mysql57-server
sudo rm -rf /opt/local/var/db/mysql57/
sudo ./build_mysql 
```

Drush
-----

To install Drush, see http://docs.drush.org/en/master/install/

Reference
---------
https://trac.macports.org/wiki/howto/MAMP

http://aaronbonner.io/post/44973182283/a-guide-to-php-mysql-and-nginx-on-macports

https://unix.stackexchange.com/questions/108174/how-to-persistently-control-maximum-system-resource-consumption-on-mac/221988

https://bertvv.github.io/notes-to-self/2015/11/16/automating-mysql_secure_installation/
