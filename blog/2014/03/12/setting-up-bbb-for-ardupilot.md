This is the steps that I took to setup a beaglebone black for development of ardupilot (aka beaglepilot)  

1. Download the latest debian image from the [beagleboard.org page](http://beagleboard.org/latest-images). I will be using the onboard emmc, so download **Debian (BeagleBone Black - 2GB eMMC) 2014-03-04**. The date might differ, but should be at least this.

2. Write it to a SD card using the command

		sudo dd bs=4M if=/path/to/BBB-eMMC-flasher-debian-7.4-2014-03-04-2gb.img of=/dev/mmblk0

3. Place the sd card in a BBB, press down the boot button, and connect it to an external power supply.  
Wait till all the 4 LEDs light up, then let go of the boot button.  
Wait for about 15-20 mins and then all the 4 LEDs will light up again.  
You now have Debian on your BBB's 2Gb emmc.

4. Next, connect your BBB to a Linux/Windows machine using the USB cord that comes with it.  
Setup an ssh connection with

		ssh root@192.168.7.2
Share your machines internet connection with the BBB. (Internet sharing in Mac OS X is broken, you can connect it to your router and then ssh)  
On Ubuntu, I do

		sudo iptables -A POSTROUTING -t nat -j MASQUERADE
		sudo echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward > /dev/null
And on the BBB running Debian, do

		/sbin/route add default gw 192.168.7.1
		echo "nameserver 8.8.8.8" >> /etc/resolv.conf
Now you should be able to

		ping google.com
		
5. Next let's run an update 

		apt-get update
		apt-get upgrade
Now, check the available space

		df -h		
Output will be something like

		Filesystem                                              Size  Used Avail Use% Mounted on
		rootfs                                                  1.7G  1.3G  286M  83% /
		udev                                                     10M     0   10M   0% /dev
		tmpfs                                                   100M  568K   99M   1% /run
		/dev/disk/by-uuid/9523ab07-4572-412c-ac11-1e9672a03fa8  1.7G  1.3G  286M  83% /
		tmpfs                                                   249M     0  249M   0% /dev/shm
		tmpfs                                                   249M     0  249M   0% /sys/fs/cgroup
		tmpfs                                                   100M     0  100M   0% /run/user
		tmpfs                                                   5.0M     0  5.0M   0% /run/lock
		/dev/mmcblk0p1                                           96M   72M   25M  75% /boot/uboot
6. Memory onboard is limited. You can get rid of a lot of packages that you won't need. Do a

		dpkg-query -l
to get the list of installed packages.  
You will have to get rid of a couple of packages because you will be
running out of space. 
Also, take a look at this
[wiki](https://wiki.debian.org/ReduceDebian#Reducing_the_size_of_the_Debian_Installation_Footprint)
upage. Lists what you don't need with Debian.
7. Following are the steps to connect the BBB to a WiFi network. I am going to be using OurLink USB WiFi dongle which is available on [Adafruit](https://www.adafruit.com/products/1012).

		lsusb
Output

		Bus 001 Device 002: ID 0bda:8176 Realtek Semiconductor Corp. RTL8188CUS 802.11n WLAN Adapter
		Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
		Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
We can see that it belongs to the RTL8188. I believe it requires the **firmware-realtek** package, which comes pre installed.  
We will also be using **wireless-tools** package, which also comes pre
installed.  
Uncomment the WiFi example in /etc/network/interfaces

		# WiFi Example
		auto wlan0
		iface wlan0 inet dhcp
		wpa-ssid "SSID"
		wpa-psk  "PASSWD"
Reboot.
You should be connected to the WiFi network now.
8. Follow the instructions from the ardupilot
   [wiki](http://dev.ardupilot.com/wiki/setting-up-sitl-on-linux/#Setting_up_SITL_on_Linux)  
   In short,

		cd ~
		git clone http://github.com/diydrones/ardupilot.git
		git clone http://github.com/tridge/jsbsim.git
		apt-get install libtool automake autoconf libexpat1-dev
		apt-get install python-matplotlib python-serial python-wxgtk2.8 python-scipy python-opencv ccache
		pip install pymavlink MAVProxy
Add this to your  ~/.bashrc

		export PATH=$PATH:$HOME/ardupilot/Tools/autotest
		export PATH=$PATH:$HOME/MAVProxy
		export PATH=$PATH:$HOME/mavlink/pymavlink/examples
		export PATH=$PATH:$HOME/jsbsim/src
		export PATH=/usr/lib/ccache:$PATH
Then

		source .bashrc
Install jsbsim (only in case of plane)

		cd ~/jsbsim
		./autogen.sh
		make
For simulating a plane run this command:

		cd ~/ardupilot/ArduPlane
		sim_arduplane.sh --console --map --aircraft test
For simulating a copter run this command:

		cd ~/ardupilot/ArduCopter
		sim_arducopter.sh --console --map --aircraft test
For simulating a rover run this command:

		cd ~/ardupilot/APMrover2
		sim_rover.sh --console --map --aircraft test

<br>
Loads more to do like figure out space restrictions, get a real time
kernel and try a 3g dongle with the BBB.

Cheers till the next time !
