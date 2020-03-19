To protect against DoS attacks on Urban Terror getstatus

# prerequisites

install gawk (the awk I had was mawk, missing strftime)
> sudo apt-get install gawk

coreutils 7.5+ (probably already there)

for stdbuf, if you can't use that see unbuffer below
> sudo apt-get install coreutils

iptables (should already be there)
sudo apt install iptables

fail2ban
> sudo apt install fail2ban

# set it up

to capture data

Create the start up file

> featherpad /home/urt/Documents/UrbanTerror43/startgsp.sh

```
#!/bin/sh

# swtich to base folder (where we want the pid file)
cd /home/urt/Documents/UrbanTerror43


# gsp log location
logfile=/home/urt/tcpdump.log

# get the pid for that instance, filter out the pid for this awk command
pid=`ps ux | awk '/tcpdump/ && !/awk/ {print $2}'`

# -n = if string is not null
if [ -n "$pid" ]; then
  echo "A GSP server with is already running!"
else
  echo "Starting GSP Server..."
  # sets new files permissions to -rw-r-----
  umask 0026
  # create the file and timestamp it
  touch gsp.pid
  # set shell resource limits
  ulimit -c unlimited
  
  while true
  do
  
    # -e = if file exists
    if [ -e "gsp.pid" ]; then
      
		# run server
		# NOTE: awk buffers a little bit, but this will write to the output file as soon as awk releases it. when traffic is low you'll notice the buffer, when traffic spikes it should write quickly
		# NOTE: if you run it manually you need to sudo
		tcpdump -nn port 27960 and udp and ip -l | stdbuf -oL awk '{ if ($8 ~ /^1[34]$/) { time = $1; ip = $3; sub(/\.[0-9]+$/, "", ip); date = strftime("%Y-%m-%d", systime()); printf("%s %s %s\n", date, time, ip); }}' > $logfile
     
    else
      exit 0
    fi
  done
fi
```

Create the stop file

> featherpad /home/urt/Documents/UrbanTerror43/stopgsp.sh

```
#!/bin/sh

# swtich to base folder (where we want the pid file)
cd /home/urt/Documents/UrbanTerror43

echo "Finding GSP process..."
# get the pid for that instance, filter out the pid for this awk command
pid=`ps ux | awk '/tcpdump/ && !/awk/ {print $2}'`

# -n = if string is not null
if [ -n "$pid" ]; then
  # remove the file
  rm gsp.pid
  echo "Killing GSP process: $pid"
  # end the process
  kill $pid
  # check to make sure
  pid=`ps ux | awk '/tcpdump/ && !/awk/ {print $2}'`
  # -n = if string is not null
  if [ -n "$pid" ]; then
    # wait 2 seconds
    sleep 2
    # check again
    pid=`ps ux | awk '/tcpdump/ && !/awk/ {print $2}'`
    # if it's still running
    if [ -n "$pid" ]; then
      # force close the process
      kill -9 $pid
    fi
  fi
else
  # it wasn't running
  echo "Could not find a server process for GSP"
fi
```

make the scripts executable
```
chmod 774 startgsp.sh
chmod 774 stopgsp.sh
```

The above will just run it once, but you may want to create a script and set that as a service.

Set up the service to run GSP

> sudo featherpad /lib/systemd/system/gsp.service

Put the contents
```
[Unit]
Description=Urban Terror UrT getStatus Protection systemd service.
Wants=network-online.target
After=urt.target syslog.target network.target

[Service]
Type=simple
User=root
Group=root
ExecStart=/bin/sh /home/urt/Documents/UrbanTerror43/startgsp.sh
ExecStop=/bin/sh /home/urt/Documents/UrbanTerror43/stopgsp.sh

[Install]
WantedBy=multi-user.target
```

Start the service
> sudo systemctl start gsp

To stop it
> sudo systemctl stop gsp

See if it's running or not
> sudo systemctl status gsp

Set it to start automatically when the system restarts
> sudo systemctl enable gsp

You can then restart the service in a cron job daily so your log doesn't get huge.

In testing it grew about 16MB / hr (384 MB / day)

Example to restart at 5:02 AM and 4:02 PM, add these lines
> sudo crontab -e

```
2 5,16 * * * systemctl restart gsp
```

Example to restart it every 4 hours
> sudo crontab -e

```
2 */4 * * * systemctl restart gsp
```

create the Urban Terror jail rule

> sudo featherpad /etc/fail2ban/jail.local

```
[urban-terror]

enabled = true
# what port to monitor, comma separated for multiple ports
port = 27960
# which protocol to monitor on the port
protocol = udp
# point to the filter file in ./filter.d/
filter = urban-terror
# set your log location accordingly
logpath = /home/urt/tcpdump.log
# number of seconds to look back at
findtime  = 60
# maximum number of hits allowed, blocked at the next attempt
maxretry = 3
# ban time
#bantime = 10m	#10m default
# ignore internal ip addresses. You may want to add the server's external IP as well, just in case
# CIDR ranges, space separated
ignoreip = 127.0.0.0/8 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16
```
create the filter file

> sudo featherpad /etc/fail2ban/filter.d/urban-terror.conf

```
# Fail2Ban filter for failure attempts in UrT (Urban Terror) 4.3
#
#

[Definition]

failregex = <HOST>
#datepattern = ^L%%Y-%%m-%%d %%H:%%M:%%S.%%f
```

check fail2ban config 

(errors would be at the top. there's a bunch of data that follows with square brackets)
> fail2ban-client -d

restart fail2ban
> sudo systemctl restart fail2ban

see fail2ban logging
> sudo tail /var/log/fail2ban.log

see ip blockage
> sudo iptables -S

a report of how many times each ip has been banned (this is for any jail, not just UrT)
> sudo awk '($(NF-1) = /Ban/){print $NF}' /var/log/fail2ban.log | sort | uniq -c | sort -n

if there's a persistent threat you can add a manual block in iptables
> iptables -A INPUT -s 130.162.64.72 -j DROP

rules are dropped when the server restarts, which is probably OK

if you truly want persistent rules, see https://www.e2enetworks.com/help/knowledge-base/how-to-block-ip-address-on-linux-server/ 

-----------------------

# unnecessary, background data

show network interfaces
> ip link show
or  (mainly look at entry 1)
> sudo tcpdump -D


for debugging, just to see some output, capture from tcpdump

status is length 14 standard, 13 from my app
> sudo tcpdump -nn port 27960 and udp and ip

```
nn = no name resolution, no port resolutiuon
port = specifies the port to monitor
udp = only capture udp
ip = only capture ipv4
-i = interface (can specify any or omit)
-l = line buffered
```

unbuffer (not needed)
> sudo apt install expect

part of coreutils 7.5 (I have 8.3)
> stdbuf -oL


fail2ban uses strftime format.  you need to double up % signs

https://docs.python.org/2/library/datetime.html#strftime-and-strptime-behavior

check fail2ban config (errors would be at the top. there's a bunch that follows)
> fail2ban-client -d

restart fail2ban
> sudo systemctl restart fail2ban

The awk script used above with comments
```
#tcpdump.awk

#sample
#14:06:25.962483 IP 51.75.224.242.45317 > 192.168.200.157.27960: UDP, length 14
# my local tool to simulate getstatus sends 13 bytes

{#main
	# awk default field separator is "any whitespace"
	
	# the 8th field is the Length
	if ($8 ~ /^1[34]$/) {
		# the first field is the Time
		time = $1;
		# the third field is Source IP with port attached with a "."
		ip = $3;
		# rip off the port, we don't want it
		sub(/\.[0-9]+$/, "", ip);
		# generate the date
		date = strftime("%Y-%m-%d", systime());
		# print output as time space ip
		printf("%s %s %s\n", date, time, ip);
	}#if
}#main
```
