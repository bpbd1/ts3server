Install TeamSpeak3 server on Ubuntu server on VPS or dedicated server. Learn how to create your own autostart script for your TeamSpeak server.

Run as root those commands to update and upgrade all installed packages on your server:
```
apt-get update
apt-get upgrade
apt-get dist-upgrade
```
Create a new user for your Teamspeak3 server (press ENTER for all questions):
```
adduser --disabled-login teamspeak
```
Login as teamspeak user:
```
cd /home/teamspeak/; su teamspeak
```
Download and extract the last version:
```
wget https://github.com/bpbd1/teamspeak3-server_linux_amd64/releases/download/ts3server/teamspeak3-server_linux_amd64-3.0.13.8.tar.bz2
tar xvfj teamspeak3-server_linux_amd64-3.0.13.8.tar.bz2
cd teamspeak3-server_linux_amd64
cp * -R /home/teamspeak
cd ..
rm -r teamspeak3-server_linux_amd64
rm -f teamspeak3-server_linux_amd64-3.0.13.8.tar.bz2
```
Start your server for the first time (save login details and secret key to claim the server in TeamSpeak app)
```
./ts3server_startscript.sh start
```
To stop the script press CTRL+C
```
exit
```
Now let's create the start script: 
```
nano /lib/systemd/system/ts3server.service
```
add in the file:
```
[Unit]
Description=Teamspeak Service
Wants=network.target

[Service]
WorkingDirectory=/home/teamspeak
User=teamspeak
ExecStart=/home/teamspeak/ts3server_minimal_runscript.sh
ExecStop=/home/teamspeak/ts3server_startscript.sh stop
ExecReload=/home/teamspeak/ts3server_startscript.sh restart
Restart=always
RestartSec=15

[Install]
WantedBy=multi-user.target
```

save the file  CTRL and X together, then Press Y).

To enable it run at server boot:
```
systemctl daemon-reload
```
Reboot the server and check if your TeamSpeak server is running:
```
systemctl enable --now teamspeak
systemctl status teamspeak
```
```
cd /etc/init.d
nano ts3server
```
```
#! /bin/sh
### BEGIN INIT INFO
# Provides:          ts3server
# Required-Start:    $syslog $time $remote_fs
# Required-Stop:     $syslog $time $remote_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: ts3server script
# Description:       Teamspeak autostart script linux by Yamiru.com
#                    as diferent user
### END INIT INFO
#
# Author:	Viktor V.  Yamiru.com
#
. /lib/lsb/init-functions

case "$1" in
  start)
    su - teamspeak -c "/opt/ts3server/ts3server_startscript.sh start"
    echo "Starting TS3 Server"
    ;;
  stop)
    su - teamspeak -c "/opt/ts3server/ts3server_startscript.sh stop"
    echo "Shutting down TS3 Server"
    ;;
  *)
    echo "Usage: /etc/init.d/ts3server {start|stop}"
    exit 1
    ;;
esac

exit 0
```
```
sudo chmod 755 /etc/init.d/ts3server
sudo update-rc.d ts3server defaults
systemctl reboot
systectl stop ts3server
systectl start ts3server
systemctl daemon-reload
sudo update-rc.d ts3server defaults
```
if not work: change file permission /chmod/ to 777 ts3server_startscript.sh & ts3server_minimal_runscript.sh

if you have in the ouput Active: active (running) your server is ready!


```
iptables -A INPUT -p udp --dport 9987 -j ACCEPT
iptables -A INPUT -p udp --sport 9987 -j ACCEPT
iptables -A INPUT -p tcp --dport 30033 -j ACCEPT
iptables -A INPUT -p tcp --sport 30033 -j ACCEPT
iptables -A INPUT -p tcp --dport 10011 -j ACCEPT
iptables -A INPUT -p tcp --sport 10011 -j ACCEPT
```


#### Enjoy your server!
