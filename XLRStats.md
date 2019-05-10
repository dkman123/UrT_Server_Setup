
## XLRStats (I did not get this site working... yet)

it needs mod-rewrite enabled
> cd /etc/apache2/mods-enabled
create a symbolic link to enable the module
> ln -s ../mods-available/rewrite.load rewrite.load

restart apache
> systemctl restart apache2

download the site from https://github.com/XLRstats/xlrstats-web-v3/releases

(hint: expand assets)

NOTE: I'll fork this to my page when I get it working

unzip it

rename it
> mv xlrstats-web-v3-3.0.0-beta.10 xlrstats
move it
> sudo mv xlrstats /var/www/html/xlrstats

make sure folders are writable by the web service (Config is not necessary for manual install)
```
cd /var/www/html/xlrstats/app
sudo chgrp www-data tmp
sudo chgrp www-data Config
```

create db for xlrstats 
```
CREATE DATABASE xlrstats CHARACTER SET utf8;
GRANT ALL ON xlrstats.* TO 'xlrstats'@'localhost' IDENTIFIED BY 'APASSWORD';
FLUSH PRIVILEGES;
```

run script
```
use xlrstats
source /var/www/html/xlrstats/app/Config/Schema/xlrstats.sql
```

set up the configs
```
cd /var/www/html/xlrstats/app/Config
cp database.php.default database.php
```
edit database.php
> cp email.php.default email.php
edit email.php

NOTE: this looks like it’s using old mail relay port 25. I don’t know that anything will allow that anymore. I don’t know if it knows how to “talk” TLS.

create and edit security.php
```
<?php
/**
 * A random string used in security hashing methods.
 * To decrypt this message use http://infoencrypt.com/
 */
Configure::write('Security.salt', 'ChangeMe!!!-ACims4H1u/sUCk2WP9gGdcwo40QdKXRtFNiOjj2fA1ktg==');

/**
 * A random numeric string (digits only) used to encrypt/decrypt strings.
 */
Configure::write('Security.cipherSeed', '123456789456123123');
```


edit core.php

find line Configure::write('Installer.enable', true);

change it to false

insert the server link

in mysql
```
use xlrstats;
show fields from servers;
INSERT INTO servers (active, gamename, servername, servername_a, dbhost, dbuser, dbpass, dbname, server_group_id, statusurl, slogan)
VALUES (1, 'UrT', 'localhost', 'CLANNAME', 'localhost', 'b3', 'B3PASSWORD', 'b3', NULL, 'none', 'www.xlrstats.com');
select * from servers;
```

register for a license key

http://www.xlrstats.com/license_server/licenses/add/free

in mysql
```
use xlrstats
update options set value = 'YOUR-LICENSE-KEY' where id = 1;
```

enable the xlr addon in b3 (this enables xlrstats in-game)

edit the plugin_xlrstats.ini to put in the xlrstats url


the cake library (at least the latest one) wants certain php libraries enabled

sudo leafpad /etc/php/7.2/apache2/php.ini

sudo leafpad/etc/php/7.2/cli/php.ini

sudo leafpad/usr/lib/php/7.2/php.ini-production

NOTE: I edited all 3 just in case

Uncomment these lines:
```
extension=intl
extension=mbstring
extension=pdo_mysql
```

to get simplexml libraries
> sudo apt install php7.2-xml


*NOTE: I tweaked a bunch of code, including two file renames
(just for my reference)*
```
cp /var/www/html/xlrstats/lib/Cake/Utility
sudo mv String.php StringCake.php
cp /var/www/html/xlrstats/lib/Cake/Core
sudo mv Object.php ObjectCake.php
```

restart apache
> systemctl restart apache2
*some stuff that’s a maybe*
```
sudo php -i | grep simplexml
sudo systemctl restart php-fpm
```

