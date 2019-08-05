## Echelon

download zip from https://github.com/dkman123/Echelon-2

follow the instructions there

-----

## not necessary (old)

install apache prereq
> sudo apt install apache2

install php prerequisites
```
sudo apt install php
sudo apt install php-mysqli
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
> sudo featherpad /etc/apache2/apache2.conf

find the line with <Directory /var/www/>

remove “Indexes” from the next line

restart apache
> sudo systemctl restart apache2


edit the php.ini file
> sudo featherpad /etc/php/7.2/apache2/php.ini

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
> sudo apt-get install php

see https://github.com/dkman123/Echelon-2/blob/master/README.md

-----

## not necessary (old echelon)

move the site folder
> sudo mv /home/urt/Documents/echelon-master/echelon /var/www/html

launch mysql
> mysql -u root -p

create db for echelon
```
CREATE DATABASE echelon CHARACTER SET utf8;
GRANT ALL ON echelon.* TO 'echelon'@'localhost' IDENTIFIED BY 'APASSWORD';
FLUSH PRIVILEGES;
```
NOTE: use different passwords for each user, but keep a log (or use a password manager)

run the sql script
```
use echelon;
source /home/urt/Documents/echelon-master/sql/echelon.sql
source /home/urt/Documents/echelon-master/sql/echelon-1.2.0.sql;
```
formerly named echelonDK-password.sql

NOTE: resets the echelon “admin” password to “123abc!@#ABC”.


> leafpad /var/www/html/echelon/Connections/inc_config.php

set the db names and passwords

a multi b3 environment is in the sample, tweak as needed

I removed all except for the first one. Set numservers to 1


-----

## not necessary

*not needed*
```
#here, just for reference
#NOTE: used this tool to convert mysql to mysqli https://github.com/philip/MySQLConverterTool
wget https://github.com/philip/MySQLConverterTool/archive/master.zip
unzip master.zip
cd MySQLConverterTool-master/GUI
php -S localhost:8000
#then visit localhost:8000
```

restart apache (pulls in php.ini changes)
> systemctl restart apache2

visit http://localhost/echelon/index.php

log in as admin/admin or admin/123abc!@#ABC

change the password (links - change my password)

NOTE: a way to find log files, when you're not sure where it lives
> lsof |grep log
