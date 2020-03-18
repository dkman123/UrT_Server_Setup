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

* set the output file accordingly *

> sudo featherpad /home/urt/Documents/UrbanTerror43/startGSP.sh

```
# NOTE: awk buffers a little bit, but this will write to the output file as soon as awk releases it. when traffic is low you'll notice the buffer, when traffic spikes it should write quickly
sudo tcpdump -nn port 27960 and udp and ip -l | stdbuf -oL awk '{ if ($8 ~ /^1[34]$/) { time = $1; ip = $3; sub(/\.[0-9]+$/, "", ip); date = strftime("%Y-%m-%d", systime()); printf("%s %s %s\n", date, time, ip); }}' > /home/urt/tcpdump.log
```

make the scripts executable
```
chmod 774 starturt.sh
chmod 774 stopurt.sh
```

The above will just run it once, but you may want to create a script and set that as a service.

Set up the service to run UrT

> sudo featherpad /lib/systemd/system/gsp.service

Put the contents
```
[Unit]
Description=Urban Terror UrT getStatus Protection systemd service.
Wants=network-online.target
After=urt.target syslog.target network.target

[Service]
Type=simple
User=gsp
Group=gsp
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

Example to restart at 5:02 AM, add this line
> sudo crontab -e
```
2 5 * * * systemctl restart gsp
```

create the Urban Terror jail rule

> sudo featherpad /etc/fail2ban/jail.local

```
[urban-terror]

enabled = true
port = 27960
protocol = udp
filter = urban-terror
# set your log location accordingly
logpath = /home/urt/tcpdump.log
findtime  = 60
maxretry = 3
#bantime = 10m	#10m default
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

# check fail2ban config 

(errors would be at the top. there's a bunch of data that follows with square brackets)
> fail2ban-client -d

# restart fail2ban
> sudo systemctl restart fail2ban



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

see fail2ban logging
> sudo tail /var/log/fail2ban.log

see ip blockage
> sudo iptables -S

The awk script used above with comments
```
#tcpdump.awk

#sample
#14:06:25.962483 IP 51.75.224.242.45317 > 192.168.200.157.27960: UDP, length 14

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
