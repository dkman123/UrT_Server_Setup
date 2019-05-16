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

NOTE: when you ctrl+c it doesn’t end the server

RClick server name - Edit Virtual Server

Set Server Name

Anti-Flood tab

change Points needed to block commands: 50 (default 150)

change Points needed to block IP: 150 (default 250)

Security tab

change Needed Security Level: 15 (default 8, one suggested 25 but takes forever to connect)

Misc tab

uncheck Enable reporting to server list (takes you off the public list)

hit Apply

hit OK


### Permissions - Server Groups

Normal

check Administrate Clients

change Client can move: Guest

change Client can kick from channel: Guest

change Client can kick from Server: Guest

check Client can grant talk power

Apply - Keep


### add a moderator group

click the + icon (add group)

name: Mod

using template: Normal

check Administrate Clients

change Client can move: Normal

change Client can kick from channel: Normal

change Client can kick from Server: Normal

change Client can ban from Server: Normal

check Client can change client descriptions

check Client can grant talk power


check Manage Groups section

check Client can view permissions

check Assign and Remove Member

Client can add user to: Normal

Apply - Keep

### create a start script

in the UrbanTerror43 I put a script to start ts3
**startts3.sh**
```
#!/bin/bash

# run TS3
> ~/Documents/teamspeak3-server_linux_amd64/ts3server_startscript.sh start
# only needed for first start
# license_accepted=1
```

make the script executable
> chmod 774 startts3.sh

NOTE: ctrl+c will NOT stop ts3
