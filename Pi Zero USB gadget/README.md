This is a guide for setting up Pi Zero USB gadgets, mostly taken from Andrew Mulholland's [excellent guide](https://blog.gbaman.info/?p=791) with a few troubleshooting bits added at the end.

If you're not familiar with Pi Zero USB gadgets, these set up the Zero in such a way that you can plug the Zero's USB data port into a PC and connect with it as if it was connected to the same network as the PC, e.g. through SSH.  You can even share the  PC's network connection so that the Zero can connect to the internet.  This creates a very simple "on the go" machine which doesn't need to be connected to WiFi to be fully functional.  

This functionality is only available on the Pi Zero (any version, including the W).  It will not work for a 2B, 3B or 3B+ because of the nature of their USB ports:  the Zero has an "on the go" (or OTG) port, which allows it to pretend to be a USB device to a PC.  In this case, it pretends to be a USB ethernet dongle, and your PC uses that to set up a "network" with the Pi zero pretends to be connected to, hence why the PC can talk to the Zero as if it was on a LAN or WiFi network.  The other models of Pi don't have this OTG functionality, and so can't do this.

So, without further ado, here's what to do:

1) On Windows PCs, you need to install [Apple's Bonjour driver](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=en_US), while for Linux and MacOS you'll need the Avahi Daemon.  This may or may not be pre-installed on your Linux/MacOS system, check your OS package managers to find out and if necessary install it.
2) Download and flash the latest Raspbian Full/Lite to your Pi's microSD card.
3) Once complete but before inserting the card into your Pi, open the card's boot partition and find the file called `config.txt`.  Open this using a basic text editor (**note**: document editors like Word are a bad choice for this, because they can insert all sorts of formatting in the background.  Use something more simple like Notepad++).  Add a new line containing `dtoverlay=dwc2` to the bottom and save it.
4) In the same partition find and open `cmdline.txt`.  **Be careful with this file**, it can be fussy about formatting and unlike many config files doesn't put different parameters on separate lines: everything is in one big, long string of text.  Only eddit what you absolutely have to.  Somewhere in that line of text is the word `rootwait`.  Anywhere after that, add the text `modules-load=dwc2,g_ether`.  There should be a single space separating `rootwait` and `modules-load=dwc2,g_ether`, and a single space between `modules-load=dwc2,g_ether` and whatever comes after it.
5)  Finally, in the boot partition of the microSD card add a file called `ssh`.  If you're using Windows do not let it add a file extension like .txt: the file should *only* be called `ssh`.
6)  Pop the card out of your PC and insert it into your Pi zero.  Plug a microUSB cabe into the Zero's **data port**  (the one labelled `USB`, not `PWR IN`), and the other end into your PC.  After the Zero boots (remember, the Zero is not the fastest machine around so this could take several minutes on the first boot) open PuTTY on your PC and try SSH into a machine with the IP address `raspberrypi.local`.  You should be able to log in and use the Pi as normal.

#Troubleshooting.

The main reason I wanted to write this article was to document some troubleshooting I've had to go through on occasion.  All of this was done for Windows 7 machines, so may not be relevant for anything else.

**If PuTTY can't find a device with the address `raspberrypi.local`**
- Make sure you've installed Bonjour (see step 1 of the guide).  This allows you to connect to a device on the local network using the destination machine's hostname rather than the IP address.

**If putty still can't find a device with that name**
Sometimes Windows has problems with USB gadgets. In particular, it seems to sometimes recognise them as the wrong type of device and/or install the wrong driver.  

1)  Go to your Start Menu and type `device manager`.  This should bring up a shortcut for the Device Manager, which you should click on.  This window lists all of the hardware devices on your PC as Windows sees them.  
2)  Click on the expanding arrow beside "Network adapters", and look at the list of network devices which you have.  If the Pi Zero USB gadget is working you should see one called "USB ethernet/ RNDIS Gadget".  
3)  If not, expand the section called "COM PORTS".  One of these may be your Pi, which has been given the wrong identity by your PC.  Try unplugging your Zero and then plugging it back in:  a device should disappear and then reappear.
4)  When you know which device is the Pi, download the appropriate [USB ethernet/RNDIS Gadget driver](http://www.catalog.update.microsoft.com/Search.aspx?q=USB%20RNDIS%20Gadget).
5) In the Device Manager, right click on the Pi Zero and hit "properties".  Go to the Drivers tab, and select "update driver".  When it asks how you want to find the driver, select "browse my computer for driver software", and select the downloaded driver file.  This should assign the correct driver to the Pi Zero, and allow your PC to talk to the Pi correctly.
