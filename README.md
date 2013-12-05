macports-lamp
=============

Builds a LAMP stack on OS X 10.9 using MacPorts.

Intended to be used on a fresh 10.9 install.

Why
---
MacPorts is powerful but finicky. I commonly end up with MySQL (I know, we're
all switching to MariaDB soon) with the wrong permissions and other little
niggling trouble. Therefore this script, which will reliably install MySQL 5.5
and PHP 5.4 on 10.9. At least, for me. Tested with MacPorts 2.2.1 on 10.9.0.

Prerequisites
-------------

Install [MacPorts](http://www.macports.org/install.php).

Create a my.cnf and put it at /etc/my.cnf. You could use the [Percona Wizard](https://tools.percona.com/wizard).

Ensure that /etc/my.cnf has a line for the socket MacPorts will use:

```
socket		= /opt/local/var/run/mysql55/mysqld.sock
```

Read Through the Script
-----------------------

Note that it will set your MySQL root password to "password" unless you
change this in the expect heredoc (ca. lines 36, 39).

Default Virtual Host
--------------------

The script will create the following virtual host which can be reached by
going to http://local.dev/ after the script completes.

```
NameVirtualHost *:80
<VirtualHost *:80>
    ServerName local.dev
    DocumentRoot /Users/username/Sites
    <Directory /Users/username/Sites>
      Options Indexes FollowSymLinks
      DirectoryIndex index.php index.html
      Order deny,allow
      Allow from 127.0.0.1
      AllowOverride All
    </Directory>
</VirtualHost>
```

Shortcuts
---------

The following functions will be installed in ~/.profile. I find them useful
at the command line:

```
te - tail Apache error log
ta - tail Apache access log
tp - tail PHP error log
acr - apachectl restart
```

Running the Script
------------------

```
sudo ./build_lamp
```

Drush
-----

If you need [Drush](http://drush.ws) (used for Drupal development):

```
sudo ./install_drush
```

Reference
---------
https://trac.macports.org/wiki/howto/MAMP

http://aaronbonner.io/post/44973182283/a-guide-to-php-mysql-and-nginx-on-macports

