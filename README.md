macports-lamp
=============

Builds a LAMP (MAMP) stack on macOS 10.13 High Sierra using MacPorts.

Intended to be used on a fresh 10.13 install. For previous versions of macOS
use the pre-high-sierra branch.

Please read this README all the way through before beginning.

Why
---
MacPorts is powerful but finicky. I commonly end up with MySQL (I know, we're
all switching to MariaDB soon) with the wrong permissions and other little
niggling trouble. Therefore this script, which will reliably install MySQL 5.5
and PHP 5.6 plus Apache 2.4 on OS X. At least, for me. Tested with MacPorts 
2.4.2 on OS X 10.13.

Prerequisites
-------------

Install [MacPorts](http://www.macports.org/install.php) with its prerequisites.

Make sure to install XCode and do

```
sudo xcodebuild -license
xcode-select --install
```

Create a my.cnf and put it at /etc/my.cnf. You could use the [Percona Wizard](https://tools.percona.com/wizard).

Ensure that /etc/my.cnf has a line for the socket MacPorts will use:

```
socket		= /opt/local/var/run/mysql55/mysqld.sock
```

Read Through the Script
-----------------------

Note that it will set your MySQL root password to "password" unless you
change this in the expect heredoc (ca. lines 36, 39 of the build_mysql script).

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
      Require ip 127.0.0.1
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

Running the Scripts
-------------------

```
sudo ./build_lamp
sudo ./build_mysql
```

Drush
-----

The install_drush script is now a historical footnote and should not be used.

Instead: http://docs.drush.org/en/master/install/

Reference
---------
https://trac.macports.org/wiki/howto/MAMP

http://aaronbonner.io/post/44973182283/a-guide-to-php-mysql-and-nginx-on-macports

