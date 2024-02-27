macports-lamp
=============

Builds a LAMP (MAMP) stack on macOS 14.3 Sonoma using MacPorts.

Intended to be used on a fresh macOS install. 

The main branch supports MySQL 8.

For versions of macOS before Sonoma use the appropriate branch.

Please read this README all the way through before beginning.

Why
---
MacPorts is powerful but finicky. I commonly end up with MySQL with
the wrong permissions and other annoyances. Therefore this script, which will
reliably install MySQL and PHP 7.3/7.4/8.1 plus Apache 2.4 on macOS. 
At least, for me. Tested with MacPorts 2.9.2 on OS X 14.3.1.

Prerequisites
-------------
If you had installed macports-lamp previously, export any MySQL databases you want to keep
(since macports-lamp will install a clean 'n' fresh MySQL).

Stop any running MacPorts services as in the following two examples. If 
these give you errors, just continue.

```
sudo port unload apache2
sudo port unload mysql8-server
```

Move the old MacPorts installation in /opt out of the way. 

If you have System Integrity Protection enabled, you may need to [disable it first](https://developer.apple.com/documentation/security/disabling_and_enabling_system_integrity_protection).

```
sudo mv /opt/local /opt/local.old
```

If you get the error "Operation not permitted" you may need to give Terminal 
Full Disk Access (System Settings / Privacy & Security / Full Disk Access).

Make sure to install XCode and do

```
sudo xcodebuild -license
xcode-select --install
```

Install [MacPorts](http://www.macports.org/install.php) with its prerequisites.

macports-lamp comes with a my.cnf file for MySQL 8 that it will place at
/etc/my.cnf. If you already had a my.cnf there it will be moved out of the way.

MySQL 8 will use the following socket:

```
socket		= /opt/local/var/run/mysql8/mysqld.sock
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
<VirtualHost *:443>
    ServerName local.test
    ServerAlias *.local.test
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
sudo ./build_ssl_cert
sudo ./build_mysql
sudo ./increase_limits
```

Switching between PHP 7.3, PHP 7.4 and PHP 8.1:

Switch to PHP 8.1:
```
sudo port select php php81
sudo /opt/local/bin/apxs -A -e -n php7 mod_php73.so
sudo /opt/local/bin/apxs -A -e -n php7 mod_php74.so
sudo /opt/local/bin/apxs -a -e -n php mod_php81.so
acr
```

Switch to PHP 7.4:
```
sudo port select php php74
sudo /opt/local/bin/apxs -A -e -n php mod_php81.so
sudo /opt/local/bin/apxs -A -e -n php7 mod_php73.so
sudo /opt/local/bin/apxs -a -e -n php7 mod_php74.so
acr
```

Switch to PHP 7.3:
```
sudo port select php php73
sudo /opt/local/bin/apxs -A -e -n php mod_php81.so
sudo /opt/local/bin/apxs -A -e -n php7 mod_php74.so
sudo /opt/local/bin/apxs -a -e -n php7 mod_php73.so
acr

```

If you disabled System Integrity Protection earlier, don't forget to [reenable it](https://developer.apple.com/documentation/security/disabling_and_enabling_system_integrity_protection). You can check if it is enabled by typing `csrutil status` in Terminal.

Troubleshooting
---------------

If the MySQL installation messed up somehow, and you want to try it again, use the 
following to clean up and retry:

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
