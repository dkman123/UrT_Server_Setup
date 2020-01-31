## TS3
TeamSpeak server setup
Download from https://www.teamspeak.com/en/downloads/#server

Extract the gz
> tar xjf teamspeak3-server_linux_amd64-3.7.1.tar.bz2

Go into the folder
> cd ~/Documents/teamspeak3-server_linux_amd64

Create empty file .ts3server_license_accepted
> touch .ts3server_license_accepted

start it
> ./ts3server_startscript.sh start license_accepted=1

Copy the key (token)

NOTE: when you ctrl+c it doesn’t end the server

Connect to the server using the TeamSpeak client

Enter the key given to you at the firsh launch

RClick server name - Edit Virtual Server

Set Server Name

Click More

## Anti-Flood tab

change Points needed to block commands: 50 (default 150)

change Points needed to block IP: 150 (default 250)

## Security tab

change Needed Security Level: 15 (default 8, one suggested 25 but takes forever to connect)

Misc tab

uncheck Enable reporting to server list (takes you off the public list)

hit Apply

hit OK


### Permissions - Server Groups

Normal

*check Administrate Clients

change Client can move: Guest

change Client can kick from channel: Guest

change Client can kick from Server: Guest

check Client can grant talk power

Apply - Keep


### add a moderator group

click the + icon (add group)

name: Mod

using template: Normal

*check Administrate Clients

change Client can move: Normal

change Client can kick from channel: Normal

change Client can kick from Server: Normal

change Client can ban from Server: Normal

check Client can change client descriptions

check Client can grant talk power



*check Assign and Remove Member

Client can add user to: Normal


*check Manage Groups section

check Client can view permissions

Apply - Keep

### create a start script

in the UrbanTerror43 I put a script to start ts3

**startts3.sh**
```
#!/bin/sh

# change to the TS3 folder
cd /home/urt/Documents/teamspeak3-server_linux_amd64

# remove the pid file, if any
rm ts3server.pid

# run TS3
# NOTE: it's script runs and exits, but leaves the TS3 server running. So we pop out without it dying
./ts3server_startscript.sh start
# only needed for first start
# license_accepted=1

```

Create the **stopts3.sh** file
```
#!/bin/sh

# change to the TS3 folder
cd /home/urt/Documents/teamspeak3-server_linux_amd64

# stop the TS3 server
./ts3server_startscript.sh stop
```

make the scripts executable
```
sudo chmod 774 startts3.sh
sudo chmod 774 stopts3.sh
```

NOTE: ctrl+c will NOT stop ts3

Set up the service to run ts3
> sudo featherpad /lib/systemd/system/ts3.service

Put the contents
```
[Unit]
Description=TS3 systemd service.
Wants=network-online.target
After=syslog.target network.target

[Service]
Type=forking
User=urt
Group=urt
ExecStart=/bin/sh /home/urt/Documents/UrbanTerror43/startts3.sh
ExecStop=/bin/sh /home/urt/Documents/UrbanTerror43/stopts3.sh
PIDFile=/home/urt/Documents/teamspeak3-server_linux_amd64/ts3server.pid

[Install]
WantedBy=multi-user.target
```

Start the service
> sudo systemctl start ts3

To stop it
> sudo systemctl stop ts3

To see if it's running or not
> sudo systemctl status ts3

Set it to start automatically when the system restarts
> sudo systemctl enable ts3

