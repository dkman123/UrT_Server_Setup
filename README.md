## UrT_Server_Setup
Urban Terror

created 2019.05.06
by isopropanol

## HOW TO READ: 
Italics are not necessary

FYI:
~ defaults to the current user’s home directory, so it translates to /home/urt here
I tried to be good about making sure correct apostrophes got into this document. Editor “fancy” apostrophes will not work in commands.
I tried to be good about correcting editor “auto-capitalization” of the first letter on a line.

------

I installed lubuntu (ubuntu with a lighter GUI)
During installation I installed 3rd party tools, just because (probably not needed)

I installed this so i could hit my network server to copy files via SMB shares
> sudo apt install cifs-utils

install prerequisites
> sudo apt-get install python-setuptools

## Other utilities

file comparison
> sudo apt install meld

file search / indexing
```
sudo add-apt-repository ppa:christian-boxdoerfer/fsearch-daily
sudo apt update 
sudo apt install fsearch-trunk
```

editor with context highlighting & tabs
if you launch it with sudo you can edit and save files in protected areas
> sudo apt install geany

--------

## UrT

add a user that will run everything
> sudo useradd -m urt

set the pw
> sudo passwd urt

add the user to sudoers
> sudo usermod -a -G sudo urt

set the default shell
> usermod --shell /bin/bash urt

switch user
> su - urt

get the urt files where you want them
...by whatever process works for you
I put it into /home/urt/Documents/UrbanTerror43

take ownership of the files
```
cd ~/Documents
sudo chown -R urt:urt UrbanTerror43/
```

reqs for UrT server (i think it was, or at least the updater)
```
sudo apt-get install libxml2-utils
sudo apt-get install curl
```

download the needed files (and update)
```
cd UrbanTerror43/
./UrTUpdater_Ded.i386
```

install guest additions to the Vitrual Box (for clipboard copy/paste)
only necessary if using Virtual Box
```
cd /media/urt/VBox_GAs_5.2.28
sudo ./VBoxLinuxAdditions.run
```

---------

a script to start UrT server, 
**starturt.sh**
```
#!/bin/bash

change dir to the script's directory
#cd $(dirname $0)
cd /home/urt/Documents/UrbanTerror43

if [ -e urt.pid ]; then
    if ( kill -0 $(cat urt.pid) 2> /dev/null ); then
        echo "The server is already running"
        exit 1
    else
        echo "urt.pid found, but no server running. Possibly your previously started server crashed"
        echo "Please view the logfile for details."
        rm urt.pid
    fi
fi

# run server
./Quake3-UrT-Ded.x86_64 +set fs_game q3ut4  +set fs_basepath ~/Documents/UrbanTerror43  +set fs_homepath /home/urt/Documents/UrbanTerror43 +set dedicated 2 +set net_port 27960 +set com_hunkmegs 128 +exec server.cfg &

# write process id
echo $! > urt.pid
```

-------

a **stopurt.sh** file
```
#!/bin/bash
# change dir to the script's directory
#cd $(dirname $0)
cd /home/urt/UrbanTerror43

#kill the process
kill $(cat urt.pid)

# tidy up
rm urt.pid
```

---------

*install screen
I didn’t personally use screen
> sudo apt-get install screen

run directly as 
```
cd ~/Documents/UrbanTerror43
./starturt.sh
```
NOTE: when you ctrl+c to get out of the command it doesn’t kill the server
top stop the server
> ./stopurt.sh

*start the server
> screen -m -d -S UrT-Server sh starturt.sh

set up the map cycle
```
cd UrbanTerror43/q3ut4
cp mapcycle_example.txt mapcycle.txt
leafpad mapcycle.txt
NOTE: to set up custom settings per map do something like this:
```

```
ut4_paris
{
    g_gear "0"
    g_gravity 800
}
```

NOTE: the settings don’t reset to default on the next map. You need to do that.
NOTE: g_gear “0” allows all gear
see the gear calculator https://www.urbanterror.info/support/180-server-cvars/#2

edit the server config
```
cd UrbanTerror43/q3ut4
cp server_example.cfg server.cfg
leafpad server.cfg
```
NOTE: sv_hostname is what shows on the server list. It does support text colors


----

## B3

download B3 zip from https://github.com/ptitbigorneau/BigBrotherBot-For-UrT43
or https://github.com/BigBrotherBot/big-brother-bot
NOTE: I used the first one, but it’s missing a folder I had to pull from the second, see below

For b3 commands see http://kzh.clankrh.com/b3/kzhzombie/commands.html
Or https://pingperfect.com/index.php/knowledgebase/94/B3-Commands.html

for B3 install mysql
```
sudo apt-get install mysql-server
sudo apt-get install python-mysqldb
```

start the service
> systemctl start mysql

launch at boot
> systemctl enable mysql

Launch the first time (if it says access denied, try with sudo. If it only works with sudo that’s a bug. See "to fix the mysql won’t connect unless i sudo it", but you can wait to fix it until that point)

run mysql
> /usr/bin/mysql -u root -p
**enter** empty password to get in

if that doesn’t work, force set the password
> sudo mysqladmin -u root password NEWPASSWORD

if that doesn’t work make a file MysqlReset.sql:
> UPDATE mysql.user SET authentication_string=PASSWORD('123abc!@#ABC') WHERE User='root';

then manually start mysqld with  
> sudo mysqld --init-file=FullPathToMysqlReset.sql

stop the service
> systemctl stop mysql

start it normally
> systemctl start mysql

change folder
> cd ~/Documents/B3/BigBrotherBot-For-UrT43-master/b3/sql/mysql

get into mysql
> mysql -u root -p

run these in this order to set up the database (in this order)
```
source b3-db.sql
use b3;
source b3.sql
source b3-update-1.3.0.sql
source b3-update-1.6.0.sql
source b3-update-1.7.0.sql
source b3-update-1.8.1.sql
source b3-update-1.9.0.sql
source b3-update-1.10.0.sql
```

in server.cfg (settings for B3)
```
set g_logsync "2"    // 0=no log, 1=buffered, 2=continuous, 3=append
set g_loghits "0"    // log hits 1 allows b3 to recognize headshots and xlrstats to provide hit location statistics (makes much bigger logs, might not be needed)
//You might want to log every bullet hit to the game log file in order for B3 to work with plugins such as XLRstats.
  set sv_minrate “25000” // to have stable pings and have B3 work smoothly
```

b3 usergroups

Lvl | keyword
--- | -------
100 | superadmin
 80 | senioradmin
 60 | fulladmin
 40 | admin
 20 | mod
  2 | reg
  1 | user
  0 | guest

NOTE: in addons you can set the levels that can run commands (in the main system too)

build B3 (from install.rst)
```
cd ~/Documents/B3/BigBrotherBot-For-UrT43-master/b3
python setup.py build
sudo python setup.py install
```
NOTE: goes to usr/local/lib/python2.7/dist-packages
NOTE: /usr/local/bin/b3_run

b3 config file (named b3.xml or b3.ini)
you can grab the sample b3.ini file in this repo or generate one by:
grab b3 config generator from https://github.com/ozguruysal/b3-config-generator
I set up WAMP on windows
download from http://www.wampserver.com/
tossed the generator files into the www folder
visited http://localhost/b3-config-generator-master/
the generator page option only went up to UrT 4.2, but that’s ok for now
downloaded b3.ini

put the ini into /home/urt/.b3/b3.ini
change the parser to 
> parser: iourt43
change the database user and password (note: i set them wrong or didn’t set them on the generator)

copy the plugin config files 
> cp ~/Documents/B3/BigBrotherBot-For-UrT43-master/b3/conf /home/urt/.b3

b3 1st run setup (from first_run.rst)
> cd ~/Documents/B3/BigBrotherBot-For-UrT43-master
#python ./b3_run.py
> /usr/local/bin/b3_run
errors on no config

got error connecting to db
to fix the mysql won’t connect unless i sudo it
connect to mysql
```
sudo mysql -u root
SELECT User, Host FROM mysql.user;
DROP USER 'root'@'localhost';
CREATE USER 'root'@'%' IDENTIFIED BY '';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

run the game (become the super admin, 1st player to type this gets it - so DO IT NOW)
> !iamgod

mysql tip: if you get error Table '{tablename}' is marked as crashed and should be repaired
> mysqlcheck -uroot -p --repair --all-databases

*not needed*
```
#Not needed: sudo apt install python-pip
#Not needed: pip install GeoIP
#I don’t think this was needed either
> sudo apt install python-geoip*


#I’m not sure that this was necessary

cd /usr/local/share
sudo mkdir GeoIP

possibly DL file from https://dev.maxmind.com/geoip/geoip2/geolite2/
but i had an older GeoIP.dat file that I sent up to the server Desktop
> sudo cp ~/Desktop/GeoIP.dat .
an old dat can also be found in older b3 github*
```

NOTE: To get a Python library location
```
import geoip
print ( geoip.__file__ )
```

*not needed*
```
#NOTE: The repo https://github.com/ptitbigorneau/BigBrotherBot-For-UrT43 is missing the folder plugins/geolocation/lib
Get the repo from https://github.com/BigBrotherBot/big-brother-bot and fill in the missing folder
#if the b3.log says it can’t find plugin geolocation
#copy the geolocation plugin to extplugins (because it’s looking for it there)
#I didn’t place this when I did it, but I think that I have this command correct.
cd /usr/local/lib/python2.7/dist-packages/b3-1.12-py2.7.egg/b3/extplugins
sudo cp -r /usr/local/lib/python2.7/dist-packages/b3-1.12-py2.7.egg/b3/plugins/geolocation /usr/local/lib/python2.7/dist-packages/b3-1.12-py2.7.egg/b3/extplugins
ls
#copy the lib folder so it becomes: 
#/usr/local/lib/python2.7/dist-packages/b3-1.12-py2.7.egg/b3/extplugins/geolocation/lib*
```

in the UrbanTerror43 I put a script to start b3
**startb3.sh**
```
#!/bin/bash

# run server
/usr/local/bin/b3_run
```


NOTE: ctrl+c will stop b3

------

## TS3
Download from https://www.teamspeak.com/en/downloads/#server
Extract the gz
> cd ~/Documents/teamspeak3-server_linux_amd64
Create empty file .ts3server_license_accepted
> touch .ts3server_license_accepted
start it
> ./ts3server_startscript.sh start license_accepted=1
NOTE: when you ctrl+c it doesn’t end the server

RClick server name - Edit Virtual Server
Set Server Name
Anti-Flood tab
change Points needed to block commands: 50 (default 100?)
change Points needed to block IP: 150 (default 250)
Security tab
change Needed Security Level: 15 (default 8, one suggested 25 but takes forever to connect)
Misc tab
uncheck Enable reporting to server list (takes you off the public list)
hit Apply

Permissions - Server Groups
Normal
check Administrate Clients
change Client can move: Guest
change Client can kick from channel: Guest
change Client can kick from Server: Guest
check Client can grant talk power
Apply - Keep

click the + ican (add group)
name: Mod
using template: Normal
check Administrate Clients
change Client can move: Normal
change Client can kick from channel: Normal
change Client can kick from Server: Normal
change Client can ban from Server: Normal
check Client can change client descriptions
check Client can grant talk power
Apply - Keep
check Manage Groups
check Client can view permissions
check Assign and Remove Member
Client can add user to: Normal
Apply - Keep

in the UrbanTerror43 I put a script to start ts3
**startts3.sh**
```
#!/bin/bash

# run TS3
> ~/Documents/teamspeak3-server_linux_amd64/ts3server_startscript.sh start
# only needed for first start
# license_accepted=1
```

NOTE: ctrl+c will NOT stop ts3

----

## Echelon

old DL from https://github.com/markweirath/echelon
new DL from https://github.com/dkman123/echelon
i don’t like the “unzip” step, don’t extract it to the root of your website

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

edit /var/www/html/echelon/Connections/inc_config.php
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
contents of phpinfo.php (do not include -- lines)
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
lsof |grep log

NOTE: apache log is in  /var/log/apache/access.log  it shows php errors

 turn off directory listing
 Open /etc/apache2/apache2.conf
 find the line with <Directory /var/www/>
 remove “Indexes” from the next line
 restart apache
> systemctl restart apache2

----

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
--
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

--

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
Open /etc/php/7.2/apache2/php.ini
/etc/php/7.2/cli/php.ini
/usr/lib/php/7.2/php.ini-production
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
(just for my reference)
```
cp /var/www/html/xlrstats/lib/Cake/Utility
sudo mv String.php StringCake.php
cp /var/www/html/xlrstats/lib/Cake/Core
sudo mv Object.php ObjectCake.php
```

restart apache
> systemctl restart apache2
*some stuff that’s a maybe
```
sudo php -i | grep simplexml
sudo systemctl restart php-fpm
```
in mysql ( to see b3 xlr data)
```
use b3;
select * from xlr_actionstats;
select * from xlr_bodyparts;
select * from xlr_history_monthly;
select * from xlr_history_weekly;
select * from xlr_mapstats;
select * from xlr_opponents;
select * from xlr_playeractions;
select * from xlr_playerbody;
select * from xlr_playermaps;
select * from xlr_playerstats;
select * from xlr_weaponstats;
select * from xlr_weaponusage;
```
