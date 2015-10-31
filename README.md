# Alternating Curent Sync
This is a tool that utilizes inotifywait to sync directories between servers.  
  
Tested on `RHEL 6`, `RHEL 7`, and `Ubuntu 15`.  

#### Things you need to know
When writing acsync, my thought process was that I didn't want to go through a lengthy process of setting up a distributed file system like `glusterfs` and I didn't want a single point of failure having a single master server (like with `lsync`). I also couldn't just install `gitfs` on CentOS server without a buttload of compiling and fixing.
- Setup is quick, dirty, and easy. It just works
- Newest file wins, just like with `lsync`
- There is no file locking like with `glusterfs`

### Todo List
- Need to add logging
- Need to add server removal when sync fails

### Installation
To get started with acsync on your servers, you will need to setup an SSH key pair that will sit on all of the servers you're syncing to. On the first server just run this:
```
# ssh-keygen -t rsa -f /root/.ssh/id_rsa -N ''
# cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```
Then sync them over to each server:
```
# rsync --delete -avz /root/.ssh/ 192.168.3.1:/root/.ssh
# rsync --delete -avz /root/.ssh/ 192.168.3.2:/root/.ssh
# rsync --delete -avz /root/.ssh/ 192.168.3.3:/root/.ssh
# rsync --delete -avz /root/.ssh/ 192.168.3.4:/root/.ssh
```

The following command will get all the neccessary files and install neccesary dependencies:
```
# curl -s https://raw.githubusercontent.com/Igknighted/acsync/master/install  | bash
```
After this, all the neccesary files for acsync will be there. We just need to configure it. There are two configuration files for this `/etc/acsync/acsync.servers` and `/etc/acsync/acsync.directories`. When you update these files on one server running acsync, it will get propagated to all the other servers automatically.  
  
The file `/etc/acsync/acsync.servers` should contain the IP addresses of each server (including the local server). For example:
```
192.168.3.1
192.168.3.2
192.168.3.3
192.168.3.4
```
  
The file `/etc/acsync/acsync.directories` should contain parent directories to be copied between servers. If you update this file, acsync needs to be restarted on every server.
```
/var/www
/home/username
```
  
Note: `The service was already set to autostart when you ran the installer`  

  
After setting up the configuration files and starting acsync, you will need to initialize the servers from the original servers. Just run this script to sync all the directories to the other servers: `/opt/acsync/initialize`

### Starting the service
For systemd:
```
systemctl start acsync
```
For RHEL with init.d:
```
service acsync start
```
For Ubuntu and Debian:
```
service acsync start
```
