put the phpbb zip file into ~/Documents

go to the documents folder
> cd ~/Documents

unzip it
> 7za x phpBB-3.2.7.zip

go to the web root
> cd /var/www/html

move the files there
> sudo mv ~/Documents/phpBB3 .

change folder permissions
```
# set group
sudo chgrp -R www-data /var/www/html/phpBB3/cache
sudo chgrp -R www-data /var/www/html/phpBB3/store
sudo chgrp -R www-data /var/www/html/phpBB3/files
sudo chgrp -R www-data /var/www/html/phpBB3/config.php
sudo chgrp -R www-data /var/www/html/phpBB3/images/avatars/upload/
# make all files readable
sudo find /var/www/html/phpBB3 -type d -exec chmod g+rx {} +
sudo find /var/www/html/phpBB3 -type f -exec chmod g+r {} +
# make certain folders writable
sudo chmod g+w /var/www/html/phpBB3/cache
sudo chmod g+w /var/www/html/phpBB3/store
sudo chmod g+w /var/www/html/phpBB3/files
sudo chmod g+w /var/www/html/phpBB3/config.php
sudo chmod g+w /var/www/html/phpBB3/images/avatars/upload/
```

install requirements (xml)
> sudo apt install php-xml

edit the config
> sudo leafpad /etc/php/7.2/apache2/php.ini

add extension=dom to the extensions section

install requirements (json)
> sudo apt-get install php-json

edit the config
> sudo leafpad /etc/php/7.2/apache2/php.ini

add extension=json to the extensions section? (I'm not sure if that's necessary)

OPTIONAL bz2
> sudo apt install php-bz2

edit the config
> sudo leafpad /etc/php/7.2/apache2/php.ini

uncomment extension=bz2

restart apache
> systemctl restart apache2

open mysql
> mysql -u root -p

create the database
```
CREATE DATABASE phpbb CHARACTER SET utf8;
GRANT ALL ON phpbb.* TO 'phpbb'@'localhost' IDENTIFIED BY '{SETPASSWORD}';
```

visit the install page
> https://YOURSITE/phpBB3

go to the install tab and **follow the steps**

visit the install page
> https://YOURSITE/phpBB3

go to the admin link on the bottom

-----

move themes to the styles folder (an example)
> sudo mv ~/Documents/phpbb/acidtech /var/www/html/phpBB3/styles

change folder permissions
```
# set group
sudo chgrp -R www-data /var/www/html/phpBB3/styles
make all files readable
sudo find /var/www/html/phpBB3/styles -type d -exec chmod g+rx {} +
sudo find /var/www/html/phpBB3/styles -type f -exec chmod g+r {} +
# make certain folders writable
sudo chmod g+w /var/www/html/phpBB3/styles
```

In the Admin Control Panel you need to Activate the theme in the Customization tab

OPTIONAL: create robots.txt (to sotp robots from scanning your site)
> sudo leafpad robots.txt

with the following contents
```
User-agent: *
Disallow: /
```
