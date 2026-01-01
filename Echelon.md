## Echelon

download zip from https://github.com/dkman123/Echelon-2

follow the instructions there

-----

install apache prereq
> sudo apt install apache2

install php prerequisites
```
#sudo apt install php php-mysqli libapache2-mod-php7.3
## or
sudo apt install php php8.1-mysqli libapache2-mod-php8.1
```

to test php and verify extensions (not necessary)
```
cd /var/www/html
sudo mkdir testphp
sudo chown urt testphp
cd testphp
featherpad ./phpinfo.php
```
contents of phpinfo.php
```
<?php

// Show all information, defaults to INFO_ALL
phpinfo();

// Show just the module information.
// phpinfo(8) yields identical results.
phpinfo(INFO_MODULES);

?>
```

perform updates (not absolutely necessary)
```
sudo apt-get update
sudo apt-get upgrade
```

NOTE: apache log is in  /var/log/apache2/access.log  it shows php errors

turn off directory listing
> sudo nano /etc/apache2/apache2.conf

find the line with <Directory /var/www/>

remove “Indexes” from the next line

restart apache
> sudo systemctl restart apache2


edit the php.ini file
> sudo nano /etc/php/8.1/apache2/php.ini

~~/etc/php/7.2/cli/php.ini~~

~~/usr/lib/php/7.2/php.ini-production~~

~~NOTE: When in doubt you can edit all 3~~

uncomment the line 
> extension=mysqli

if you need to debug echelon (or anything php)

Change display_errors to On (for normal operation you probably want this Off for security)
> display_errors = On


Visit http://localhost/testphp/phpinfo.php

it should work

if it does you can rename phpinfo.php to phpinfo.phpX (for security reasons)
```
cd /var/www/html/testphp
sudo mv phpinfo.php phpinfo.phpX
```

if you get a blank white page make sure php installed (you're hitting a php error)
> sudo apt install php php-mysqli libapache2-mod-php8.1

see https://github.com/dkman123/Echelon-2/blob/master/README.md

-----

To enable/disable apache modules
```
a2enmod {module}
a2dismod {module}
# example
a2enmod php8.1
```

To create the database
```
CREATE DATABASE echelon;
USE echelon;
```

If you need to create the user, replace NEWPASSWORD
```
DROP USER 'echelon'@'loclahost';
CREATE USER 'echelon'@'localhost' IDENTIFIED WITH mysql_native_password BY 'NEWPASSWORD';
GRANT ALL PRIVILEGES ON echelon.* TO 'echelon'@'localhost';
FLUSH PRIVILEGES;
```

You can creae the DB structure by running the sql files, or restore from a backup
```
SOURCE echelonBackup.sql
```
