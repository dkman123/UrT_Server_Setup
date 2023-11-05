Direct download B3 zip from https://github.com/dkman123/BigBrotherBot-For-UrT43

~~download B3 zip from https://github.com/ptitbigorneau/BigBrotherBot-For-UrT43~~

~~or https://github.com/BigBrotherBot/big-brother-bot~~

~~NOTE: I used the first one, but it’s missing a folder I had to pull from the second, see below~~

install prereq mysql
```
sudo apt-get install python-mysqldb
```

~~On 20.04 python-mysqldb is not available any more. To get it you can add the older repo~~
```
~~sudo add-apt-repository 'deb http://archive.ubuntu.com/ubuntu bionic main'
~~sudo apt update
~~sudo apt install -y python-mysqldb
```
.
~~trying to install via pip~~
```
~~sudo add-apt-repository universe
~~sudo apt update 
~~sudo apt install python2
~~curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
~~sudo python2 get-pip.py
~~pip2 --version
~~sudo pip2 install MySQL-python
```

----

## Unnecessary steps I'm just including in case something comes up again.


~~Move the geolocation plugin~~
> cd /usr/local/lib/python2.7/dist-packages/b3-1.12-py2.7.egg/b3/extplugins
~~Copy the folder~~
> sudo cp -r ../plugins/geolocation .

~~hit enter as an empty password to get in~~

~~if that doesn’t work, force set the password~~
> sudo mysqladmin -u root password NEWPASSWORD

~~if that doesn’t work make a file MysqlReset.sql (replace {NEWPASSWORD}):~~
> UPDATE mysql.user SET authentication_string=PASSWORD('{NEWPASSWORD}') WHERE User='root';

~~then manually start mysqld with~~
> sudo mysqld --init-file=FullPathToMysqlReset.sql

~~stop the service~~
> systemctl stop mysql

~~start the service~~
> systemctl start mysql

~~b3 1st run setup (from first_run.rst)~~
> cd ~/Documents/B3/BigBrotherBot-For-UrT43-master
~~*#python ./b3_run.py*~~
> /usr/local/bin/b3_run
~~errors on no config~~

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
