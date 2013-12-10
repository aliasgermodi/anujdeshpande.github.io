Installing Arch Linux on a Rasbperry Pi is a very well documented process.
This tutorial assumes that you have an Arch Linux running on the Pi

Check your partitions. You should have 3(1, 2 and 5)

fdisk -l /dev/mmcblk0

Time to resize partitions to occupy the complete SD card.

fdisk /dev/mmcblk0

Now delete extended, logical. Create extended, logical.

sync
reboot
resize2fs /dev/mmcblk0p5

Check whether changes have taken place

df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        15G  795M   14G   6% /
devtmpfs         83M     0   83M   0% /dev
tmpfs           231M     0  231M   0% /dev/shm
tmpfs           231M  244K  231M   1% /run
tmpfs           231M     0  231M   0% /sys/fs/cgroup
tmpfs           231M     0  231M   0% /tmp
/dev/mmcblk0p1   90M   24M   67M  27% /boot

For a 16 Gb SD card, you'll see something like this.
Update

pacman -Syu

Now we have a basic Arch Linux running on Pi.

Git Server

Everyone loves GitHub. But some projects have to be private, and what better than a RPi on my local network to avoid GitHubs private repo pricing.

Getting git on a server is cakewalk, assuming you don't want complicated read/write permissions for different users. In this setup, I am assuming that you will be using ssh for transfer. Other possible candidates are Https, local file system or the git protocol. SSH is the way to go because it's all fairly common to everyone.

First off, decide the place where you want to have the repos stored. I am going to be having them in /opt/repos folder. In that let's create a project.git folder. 

On the raspberry pi, do the following :
1. Initialize it as a bare, shared git repo.

	   git init --bare --shared

2. Once you have done this, there is nothing much to do apart from setting a static IP to this Pi. But you've probably already did that. 
   	    
	    ip addr add 192.168.1.2/24 dev eth0

To this at boot check out how to use netctl in Arch.
/etc/netctl/examples consists of some examples that you can look at. 
Copy them over to /etc/netctl/your_profile.
netctl enable your_profile

On your development machine, do the follwing.

1. Inside your project folder

   	  git init
	  git add .
	  git commit -m 'Initial commit'
	  git remote add origin root@192.168.1.2:/opt/repos/project.git
	  git push origin master

2. That's it. It will work now. Everyone who'll want to push/pull to this repo will need the root password. To workaround this you can create a user known as git or anything else on the RPi and then give him read/write rights on the folder /opt/repos.
There are some pretty decent softwares which let you manage users like this. 

What I haven't covered here is 
* what's a bare git repo
* what's tools can we use to manage users
* what's the shared flag


Setting up a transmission torrent client on Arch Linux

1. Install transmission-cli from pacman repositories.

   	   pacman -S transmission-cli
There are a couple of other downloads, but we don't really need them here.

2. Next thing is editing the default settings for transmission
   
	nano .config/transmission-daemon/settings.json
	
In this file we need to change the rpc settings. Here's how it should like

    "rpc-authentication-required": false, 
    "rpc-bind-address": "0.0.0.0", 
    "rpc-enabled": true, 
    "rpc-password": "{645c9043e23ffce609ddc784d81669946b345714oBUrCnx2", 
    "rpc-port": 9091, 
    "rpc-url": "/transmission/", 
    "rpc-username": "home", 
    "rpc-whitelist": "127.0.0.1,192.168.2.*", 
    "rpc-whitelist-enabled": true, 

Save this. 

3. Start the trasnmission-daemon
   
	transmission-daemon

4. From a browser on another device on the network, go to 
   
   192.168.1.2:9091

That's it. Bookmark this link if you like :)