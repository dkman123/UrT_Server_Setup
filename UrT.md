## UrT
Urban Terror server setup

add a user that will run everything
> sudo useradd -m urt

set the pw
> sudo passwd urt

add the user to sudoers
> sudo usermod -a -G sudo urt

set the default shell
> sudo usermod --shell /bin/bash urt

logout and log in as urt

get the urt files where you have them

...by whatever process works for you
If you don't have them get them from https://www.urbanterror.info/downloads/current/

I put the extracted files into /home/urt/Documents/UrbanTerror43

take ownership of the files
```
cd ~/Documents
sudo chown -R urt:urt UrbanTerror43/
```

get up to date
```
sudo apt update
sudo apt upgrade
```

reqs for UrT server (i think it was, or at least the updater)
```
sudo apt-get install libxml2-utils
sudo apt-get install curl
```

download the needed files (and update)
```
cd UrbanTerror43/
chmod +x ./UrTUpdater_Ded.sh
./UrTUpdater_Ded.sh
```
substitute ./UrTUpdater_Ded.i386 for 32 bit systems

---------

a script to start UrT server, **starturt.sh**
```
#!/bin/sh

# swtich to base folder (where we want the pid file)
cd /home/urt/Documents/UrbanTerror43

# gets teh current folder without full path
nick=${PWD##*/}

# get the pid for that instance, filter out the pid for this awk command
pid=`ps ux | awk '/urt_nickname '"$nick"'/ && !/awk/ {print $2}'`

# -n = if string is not null
if [ -n "$pid" ]; then
  echo "A UrT server with nickname '$nick' is already running!"
else
  echo "Starting UrT Server nicknamed '$nick'..."
  # sets new files permissions to -rw-r-----
  umask 0026
  # create the file and timestamp it
  # not a true process id file, more of a mutex (mutually exclusive) file (meaning run only once)
  touch urt.pid
  # set shell resource limits
  ulimit -c unlimited
  while true
  do
    # -e = if file exists
    if [ -e "urt.pid" ]; then
      # this was a special process file we don't have
      # if [ -e "./q3ut4/logs/qconsole.log" ]; then
      # rename the file appending a last modified timestamp
      #   mv -f "./q3ut4/logs/qconsole.log" "./q3ut4/logs/qconsole.log.$(stat --printf='%Y' ./q3ut4/logs/qconsole.log).log"
      # fi

      # remove old log files (mtime is days)
      find ./q3ut4/logs -type f -name '*.log' -mtime +65 -exec rm {} \;

      # rename the old log file
      if [ -e "./q3ut4/games.log" ]; then
      # rename the file appending a last modified timestamp
        mv -f "./q3ut4/games.log" "./q3ut4/logs/games.$(stat --printf='%Y' ./q3ut4/games.log).log"
      fi

      ./Quake3-UrT-Ded.x86_64 +set fs_game q3ut4  +set urt_nickname $nick +set fs_basepath /home/urt/Documents/UrbanTerror43  +set fs_homepath /home/urt/Documents/UrbanTerror43 +set dedicated 2 +set net_port 27960 +set com_hunkmegs 128 +exec server.cfg
    else
      exit 0
    fi
  done
fi

```

-------

a **stopurt.sh** file. in the end you won't actually use this, but create it for testing
```
#!/bin/sh

# swtich to base folder (where we want the pid file)
cd /home/urt/Documents/UrbanTerror43

# gets teh current folder without full path
nick=${PWD##*/}

echo "Finding UrT process with nickname '$nick'..."
# get the pid for that instance, filter out the pid for this awk command
pid=`ps ux | awk '/urt_nickname '"$nick"'/ && !/awk/ {print $2}'`

# -n = if string is not null
if [ -n "$pid" ]; then
  # remove the file
  rm urt.pid
  echo "Killing UrT process: $pid"
  # end the process
  kill $pid
  # check to make sure
  pid=`ps ux | awk '/urt_nickname '"$nick"'/ && !/awk/ {print $2}'`
  # -n = if string is not null
  if [ -n "$pid" ]; then
    # wait 2 seconds
    sleep 2
    # check again
    pid=`ps ux | awk '/urt_nickname '"$nick"'/ && !/awk/ {print $2}'`
    # if it's still running
    if [ -n "$pid" ]; then
      # force close the process
      kill -9 $pid
    fi
  fi
else
  # it wasn't running
  echo "Could not find a server with nickname '$nick'"
fi
```

make the scripts executable
```
chmod 774 starturt.sh
chmod 774 stopurt.sh
```

---------

set up the map cycle
```
cd ~/Documents/UrbanTerror43/q3ut4
cp mapcycle_example.txt mapcycle.txt
featherpad ./mapcycle.txt
NOTE: to set up custom settings per map do something like this:
```

```
ut4_paris
{
    g_gear "0"
    g_gravity 800
}
```
NOTE: If you copy your maps up from a client don't put them in a "download" folder, put them directly into q3ut4

NOTE: The settings don’t reset to default on the next map. You need to do that.

NOTE: To reset to allow all gear use  **g_gear "0"**

see the gear calculator https://www.urbanterror.info/support/180-server-cvars/#2

edit the server config
```
cd ~/Documents/UrbanTerror43/q3ut4
cp server_example.cfg server.cfg
featherpad ./server.cfg
```
NOTE: sv_hostname is what shows on the server list. It does support some, but not all, text colors

add the logs folder

set up the map cycle
```
cd ~/Documents/UrbanTerror43/q3ut4
mkdir logs
```

-----

Set up the service to run UrT
```
sudo featherpad /lib/systemd/system/urt.service
```
Put the contents
```
[Unit]
Description=Urban Terror UrT systemd service.
Wants=network-online.target
After=syslog.target network.target

[Service]
Type=simple
User=urt
Group=urt
ExecStart=/bin/sh /home/urt/Documents/UrbanTerror43/starturt.sh
ExecStop=/bin/sh /home/urt/Documents/UrbanTerror43/stopurt.sh

[Install]
WantedBy=multi-user.target
```
Start the service
> sudo systemctl start urt

Stop it
> sudo systemctl stop urt

See if it's running or not
> sudo systemctl status urt

Set it to start automatically when the system restarts
> sudo systemctl enable urt

-----

If you're going to host map downloads on your site you may run into an issue with the windows client not being able to download over https.

They might get an Invalid protocol 0 error.

If you tell the server.cfg to download from http but your site redirects to https they'll get an error 301 message.

To fix this you need to tell the server to NOT redirect if the URL is to download a map.

These steps assume you've gone through setting up apache already (see Echelon / phpbb setup)

In apache2 edit your config file, for example 
>sudo featherpad /etc/apache2/sites-enabled/000-default.conf

go to the bottom, you'll find something like this:
```
RewriteEngine on
RewriteCond %{SERVER_NAME} =<<YOURSERVERNAME>>
RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
```

where <<YOURSERVERNAME>> is your site

add a line just below RewriteEngine on
> RewriteRule (q3ut4)($|/) - [L]

so it now should look like this
```
RewriteEngine on
RewriteRule (q3ut4)($|/) - [L]
RewriteCond %{SERVER_NAME} =<<YOURSERVERNAME>>
RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
```
Assuming you're not hosting a whole lot of urls that contain the character combination "q3ut4" that should allow UrT to download over http and the rest of your site to redirect to https. 

-----

## not neccessary

*install screen*
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

*start the server*
> screen -m -d -S UrT-Server sh starturt.sh

I installed this so i could hit my network server to copy files via SMB shares
```
sudo apt install cifs-utils
```

install prerequisites
```
sudo apt-get install python-setuptools
```

file comparison
```
sudo apt install meld
```

file search / indexing
```
sudo add-apt-repository ppa:christian-boxdoerfer/fsearch-daily
sudo apt update 
sudo apt install fsearch-trunk
```
