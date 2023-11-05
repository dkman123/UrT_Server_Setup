
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
