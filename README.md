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

*I installed lubuntu (ubuntu with a lighter GUI)*

*During installation I installed 3rd party tools, just because (probably not needed)*

*I installed this so i could hit my network server to copy files via SMB shares*
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
> sudo apt install featherpad
and as a backup
> sudo apt install geany

compressed file extraction
> sudo apt install p7zip full

*install guest additions to the Vitrual Box (for clipboard copy/paste)*

*only necessary if using Virtual Box*
```
cd /media/urt/VBox_GAs_5.2.28
sudo ./VBoxLinuxAdditions.run
```

NOTE: for VirtualBox, set the network to Bridged Adapter

--------

## UrT
https://github.com/dkman123/UrT_Server_Setup/blob/master/UrT.md

----

## B3
https://github.com/dkman123/UrT_Server_Setup/blob/master/B3.md

------

## TS3
https://github.com/dkman123/UrT_Server_Setup/blob/master/TS3.md

----

## Echelon
https://github.com/dkman123/UrT_Server_Setup/blob/master/Echelon.md

----

## XLRStats (I did not get this site working. it isn't necessary for in-game stats)
https://github.com/dkman123/UrT_Server_Setup/blob/master/XLRStats.md

