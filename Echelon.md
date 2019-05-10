## Echelon

old DL from https://github.com/markweirath/echelon

new DL from https://github.com/dkman123/echelon

I don’t like the “unzip” step, don’t extract it to the root of your website

install apache
> sudo apt install apache2

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
source /home/urt/Documents/echelon-1.1.0/sql/echelon-1.2.0.sql;
```
formerly named echelonDK-password.sql

NOTE: resets the echelon “admin” password to “123abc!@#ABC”.


> leafpad /var/www/html/echelon/Connections/inc_config.php

set the db names and passwords

a multi b3 environment is in the sample, tweak as needed

I removed all except for the first one. Set numservers to 1

install prerequisites
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
leafpad phpinfo.php
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

Visit http://localhost/testphp/phpinfo.php

it should work

if it does you can rename phpinfo.php to phpinfo.phpX (for security reasons)

you can tweak the look & feel by editing samples in /var/www/html/echelon/css

Open /etc/php/7.2/apache2/php.ini

MAYBE /etc/php/7.2/cli/php.ini

MAYBE /usr/lib/php/7.2/php.ini-production

NOTE: I edited all 3 just in case

uncomment the line 
> extension=mysqli

if you need to debug echelon (or anything php)

Change display_errors to On (for normal operation you probably want this Off for security)
> display_errors = On

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

NOTE: apache log is in  /var/log/apache/access.log  it shows php errors

**Turn off directory listing**

> sudo leafpad /etc/apache2/apache2.conf

find the line with <Directory /var/www/>

remove “Indexes” from the next line

restart apache
> systemctl restart apache2
