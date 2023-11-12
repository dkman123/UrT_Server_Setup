
## not necessary (old echelon)

Example snippet of output during "sudo python setup.py install"
```
Installed /usr/local/lib/python2.7/dist-packages/chardet-3.0.4-py2.7.egg
Searching for certifi>=2017.4.17
Reading https://pypi.org/simple/certifi/
Downloading https://files.pythonhosted.org/packages/98/98/c2ff18671db109c9f10ed27f5ef610ae05b73bd876664139cf95bd1429aa/certifi-2023.7.22.tar.gz#sha256=539cc1d13202e33ca466e88b2807e29f4c13049d6d87031a3c110744495cb082
Best match: certifi 2023.7.22
Processing certifi-2023.7.22.tar.gz
Writing /tmp/easy_install-STUlGI/certifi-2023.7.22/setup.cfg
Running certifi-2023.7.22/setup.py -q bdist_egg --dist-dir /tmp/easy_install-STUlGI/certifi-2023.7.22/egg-dist-tmp-5mMrm4
/usr/lib/python2.7/dist-packages/setuptools/dist.py:476: UserWarning: Normalizing '2023.07.22' to '2023.7.22'
  normalized_version,
warning: no previously-included files found matching '.github/'
warning: manifest_maker: MANIFEST.in, line 4: 'recursive-exclude' expects <dir> <pattern1> <pattern2> ...

  File "build/bdist.linux-x86_64/egg/certifi/core.py", line 17
    def where() -> str:
                ^
SyntaxError: invalid syntax
```
NOTE: you can browse to the Reading address to see what versions are available

----

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
