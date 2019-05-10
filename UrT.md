## UrT
Urban Terror server setup

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

