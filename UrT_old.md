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
