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

install requirements (xml, json, OPTIONAL bz2)
```
sudo apt install php-xml php-json php-bz2
```

edit the config
> sudo featherpad /etc/php/7.2/apache2/php.ini

add **extension=dom** to the extensions section

add **extension=json** to the extensions section? (I'm not sure if that's necessary)

uncomment **extension=bz2**

restart apache
> sudo systemctl restart apache2

open mysql
> mysql -u root -p

create the database
```
CREATE DATABASE phpbb CHARACTER SET utf8;
GRANT ALL ON phpbb.* TO 'phpbb'@'localhost' IDENTIFIED BY '{SETPASSWORD}';
```

visit the install page https://YOURSITE/phpBB3
> http://localhost/phpBB3

go to the install tab and **follow the steps**
```
db type: mysql with mysqli
db server: 127.0.0.1
db port: leave blank
db username: phpbb
db password: what you just set above
db name: phpbb
prefix for tables: phpbb_
```
```
Enable board-wide emails: enabled
Use SMTP: Yes
SMTP server address: smtp.gmail.com
SMTP server port: 587
Auth method: login
NOTE: set up a user specifically for the site/forum
SMTP username:
SMTP password: 
```

visit the install page https://YOURSITE/phpBB3
> http://localhost/phpBB3

go to the Admin Control Panel link on the bottom

-----

move themes to the styles folder (an example)
> sudo mv ~/Documents/phpbb/acidtech /var/www/html/phpBB3/styles

change folder permissions
```
# set group
sudo chgrp -R www-data /var/www/html/phpBB3/styles
# make all files readable
sudo find /var/www/html/phpBB3/styles -type d -exec chmod g+rx {} +
sudo find /var/www/html/phpBB3/styles -type f -exec chmod g+r {} +
# make certain folders writable
sudo chmod g+w /var/www/html/phpBB3/styles
```

In the Admin Control Panel you need to Activate the theme in the Customization tab

OPTIONAL: create robots.txt (to sotp robots from scanning your site)
> sudo featherpad /var/www/html/phpBB3/robots.txt

with the following contents
```
User-agent: *
Disallow: /
```

Remove the install folder
```
cd /var/www/html/phpBB3
sudo rm -r install
```

I had an error when trying to post, so I needed to follow the steps described here
~~https://github.com/dkman123/phpBB3_fix/tree/master~~

php 7.2 has an issue that causes an ugly posting error, something about a parameter needs to be of type DOMElement, but null was given.

this is resolved by moving to PHP 7.3

to install PHP 7.3: source https://computingforgeeks.com/how-to-install-php-7-3-on-ubuntu-18-04-ubuntu-16-04-debian/
```
# add the repo
sudo add-apt-repository ppa:ondrej/php
# above will make you hit enter, so you can't paste the whole blob
sudo apt-get update
```

add program sources
> sudo featherpad /etc/apt/sources.list

add to the bottom  (bionic, disco, whatever your version is)
```
# PHP 7.3 sources
deb http://ppa.launchpad.net/ondrej/php/ubuntu disco main
# deb-src http://ppa.launchpad.net/ondrej/php/ubuntu disco main
```

install
```
sudo apt-get install php7.3
# after installing 7.3
sudo apt install php7.3-xml php7.3-json php7.3-bz2 php7.3-mysqli
```
update the new ini file
> sudo featherpad /etc/php/7.3/apache2/php.ini

add **extension=dom** to the extensions section

add **extension=json** to the extensions section? (I'm not sure if that's necessary)

uncomment **extension=bz2**

uncomment **extension=mysqli**

if you run into problems edit the ini and set 

display_errors = On

NOTE: it's not recommended to leave display_errors On in production

change your active version of PHP
```
sudo a2dismod php7.2
sudo a2enmod php7.3

# set the default version for the cli
sudo update-alternatives --set php /usr/bin/php7.3

# restart apache
sudo systemctl restart apache2
```

do an upgrade
```
sudo apt update
# needs to be run as two steps
sudo apt upgrade
```

should already be the newest
> sudo apt install libapache2-mod-php7.3

restart apache
> sudo systemctl restart apache2

I'm missing something that I did to get it working...

reboot? -- nope

-----

these should set automatically, included just in case
```
sudo update-alternatives --set php /usr/bin/php7.3
sudo update-alternatives --set phar /usr/bin/phar7.3
sudo update-alternatives --set phar.phar /usr/bin/phar.phar7.3
sudo update-alternatives --set phpize /usr/bin/phpize7.3
sudo update-alternatives --set php-config /usr/bin/php-config7.3
```
