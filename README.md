# Alternating Curent Sync
This is a tool that utilizes inotifywait to sync directories between servers.  
  
Tested on `RHEL 6`, `RHEL 7`, and `Ubuntu 15`.  
  
# Installation
The following command will get all the neccessary files and install neccesary dependencies:
```
# curl -s https://raw.githubusercontent.com/Igknighted/acsync/master/install  | bash
```
After this, all the neccesary files for acsync will be there. We just need to configure it. There are two configuration files for this `/etc/acsync.servers` and `/etc/acsync.directories`. When you update these files on one server running acsync, it will get propagated to all the other servers automatically.  
  
The file `/etc/acsync.servers` should contain the IP addresses of each server (including the local server). For example:
```
192.168.3.1
192.168.3.2
192.168.3.3
192.168.3.4
```
  
The file `/etc/acsync.directories` should contain parent directories to be copied between servers. If you update this file, acsync needs to be restarted on every server.
```
/var/www
/home/username
```
  
Now we will need to start the service and configure it to autostart.  
For systemd:
```
systemctl enable acsync
systemctl start acsync
```
For RHEL with init.d:
```
service acsync start
chkconfig acsync on
```
For Ubuntu and Debian:
```
service acsync start
update-rc.d acsync defaults
```

  
After setting up the configuration files and starting acsync, you will need to initialize the servers from the original servers. Just run this script to sync all the directories to the other servers: `/opt/acsync/initialize`
