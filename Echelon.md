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
DROP USER 'echelon'@'localhost';
CREATE USER 'echelon'@'localhost' IDENTIFIED WITH mysql_native_password BY 'NEWPASSWORD';
GRANT ALL PRIVILEGES ON echelon.* TO 'echelon'@'localhost';
FLUSH PRIVILEGES;
```

You can creae the DB structure by running the sql files, or restore from a backup
```
SOURCE echelonBackup.sql
```

If you get "ERROR 1819 (HY000): Your password does not satisfy the current policy requirements"

You can make your password more secure or tell it to obey and get out of your way.
```
SHOW VARIABLES LIKE 'validate_password%';
SET GLOBAL validate_password.policy=LOW;
```

Attempt using the install setup
http://localhost/echelon/install/

Copy the echelon (inner) folder to your /var/www/html folder
```
# Remove the install folder
rm -R install
# Hide the phpinfo file
mv phpinfo.php phpinfo.phpX
```

Copy the sample config, then edit it
```
cp config_sample.php config.php
sudo nano config.php
```

Set the $path to your echelon folder within your html folder.

Set your DBL_PASSWORD to your ecehlon password. Set the username and DB too if you changed either.

Set the SALT to 14 random alphanumeric characters (capitals, lowercase, numbers).

Set the SES_SALT to 6 random alphanumeric characters (capitals, lowercase, numbers).

If you restored your database those should be set to whatever the old values were.

If you're using ProxyCheck.io set your PROXYCHECKioKEY.

Set your email server settings.

If you want to use IP Intel links for IP checks set the $getipintel_email.

If you want to use the server actions page, set the $server_action_path folder for it to write to.

Set the path to your map cycle file in $mapcycleFile.

Save it, CTRL+S and exit CTRL+X

Visit the page in a browser
http://localhost/echelon/index.php
