*19th January '14*

Some of the methods that I think can be used to setup duplex communication between an Android application and a Beaglebone Black. 

1. Android uses the mtp filesystem. You can setup libmtpfs and mtptools on your BeagleBone Black. Then connect the Android device to the USB host port of BBB, mount the Android filesystem in Angstrom and read/write on files there. 
Problem with this method is there is no recipe for libmtp in the Angstrom kernel tree [here](https://github.com/Angstrom-distribution/meta-oe)
OpenEmbedded has support for mtp [here](https://github.com/openembedded/meta-oe/tree/41748a4afc494b714315ba529f138214f80d41b9/meta-oe/recipes-connectivity/libmtp). So it *might* not be a difficult thing to get mtp to play along with a BBB.

2. Android has the AOA protocol(Android Open Accessory Protocol). It basically allows one to connect an Android device to an Arduino. Look at [ADK](http://developer.android.com/tools/adk/index.html)
Rowboat project has [implemented the protocol](http://code.google.com/p/rowboat/wiki/AccessoryDevKit) for the BeagleBone. According to me there isn't any difference between BBB and BeagleBone White when it comes to this, but I was unsuccessful while getting the rowboat project to work on the Black. 

3. Bluetooth : You could attach a USB dongle like [this](http://www.adafruit.com/products/1327) to your BBB. bluez on Linux allows you to pair devices too.  

4. Similar to method number 1, we have a protocol called PTP (Picture Transfer Protocol). PTP makes your Android device appear as a camera. [PTP is a subset of MTP](http://developer.android.com/reference/android/mtp/package-summary.html) .  I was able to use gphoto2, a tool based on PTP, to get and put files on an Android device connected to the Beaglebone Black.  

Basic outline is as follows :  

	opkg update
	opkg install libusb-@-dev #Replace @ with the latest version number

libusb comes pre installed with Angstrom. We need the -dev version to build libgphoto2 and gphoto2.  

Next fetch the tars available on the sourceforge pages

* [libgphoto2](http://sourceforge.net/projects/gphoto/files/)
* [gphoto2](http://sourceforge.net/projects/gphoto/files/gphoto/) (Look at the links at the bottom of the page)  

Untar both the files, and run the following commands in each.

	./configure
	make
	make install

First install, libgphoto, followed by the gphoto tool.
Now you should be able to use gphoto2 tool from the command line on the beaglebone black.  
Next we look at some of the commonly used options with gphoto2.

* --list-ports  
If you installed libusb correctly, running gphoto2 with this option will show you 6 results. We are interested in the one that reads USB.

* --auto-detect  
This will detect the cameras (in our case the Android device), that's connected to the beagle. It will show it's model number.

* --list-files  
This will list all the files that are present on the mounted device.

A comprehensive list can be found at the [man page](http://www.gphoto.org/doc/manual/ref-gphoto2-cli.html).  
This neat setup should help in a lot of projects that I have in mind :)

#### - Anuj Deshpande
