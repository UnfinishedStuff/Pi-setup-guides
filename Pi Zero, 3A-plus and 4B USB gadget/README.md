This is a guide for setting up Pi Zero, Pi 3A+ and Pi 4B USB gadgets, mostly taken from Andrew Mulholland's [excellent guide](https://blog.gbaman.info/?p=791) with a few troubleshooting bits added at the end.  It was updated on 06/01/2019 to work with the 3A+: thanks to thagrol on [the Raspberry Pi forum](https://www.raspberrypi.org/forums/viewtopic.php?t=228267) for pointing out that `config.txt` now also needs ",dr_mode=peripheral" to work properly; and on 30/06/19 to confirm that it works with the 4B and to give some notes on the power requirements of the 4B.

If you're not familiar with Pi Zero/3A+/4B USB gadgets, these set up the Pi in such a way that you can plug the board's USB data port into a PC and connect with it as if it was connected to the same network as the PC, e.g. through SSH.  You can even share the  PC's network connection so that the Pi can connect to the internet.  This creates a very simple "on the go" machine which doesn't need to be connected to WiFi to be fully functional.  

This functionality is only available on the Pi Zero (any version, including the W) and the Pi 3A+.  It will not work for a 2B, 3B or 3B+ because of the nature of their USB ports:  the Zero, 3A+ and 4B have an "on the go" (or OTG) port, which allows them to pretend to be a USB device to a PC.  In this case, the board pretends to be a USB ethernet dongle, and your PC uses that to set up a "network" which the Pi pretends to be connected to, hence why the PC can talk to the the Pi as if it was on a LAN or WiFi network.  The other models of Pi don't have this OTG functionality, and so can't do this.

So, without further ado, here's what to do:

1) On Windows PCs, you need to install [Apple's Bonjour driver](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=en_US), while for Linux and MacOS you'll need the Avahi Daemon.  This should be pre-installed on many Linux distros, check your package manager to find out and if necessary install it.  If you're using Apt to manage packages, try `apt list --installed | grep "avahi-daemon"` and see if the daemon appears in the list of installed software.
2) Download and flash the latest Raspbian Full/Lite to your Pi's microSD card.
3) Once complete but before inserting the card into your Pi, open the card's boot partition and find the file called `config.txt`.  Open this using a basic text editor (**note**: document editors like Word are a bad choice for this, because they can insert all sorts of formatting in the background.  Use something more simple like Notepad++ on Windows or Nano on Linux).  Add a new line containing `dtoverlay=dwc2,dr_mode=peripheral` to the bottom and save it.
4) In the same partition find and open `cmdline.txt`.  **Be careful with this file**, it can be fussy about formatting and unlike many config files doesn't put different parameters on separate lines: everything is in one big, long string of text.  Only edit what you absolutely have to.  Somewhere in that line of text is the word `rootwait`.  Anywhere after that, add the text `modules-load=dwc2,g_ether`.  There should be a single space separating `rootwait` and `modules-load=dwc2,g_ether`, and a single space between `modules-load=dwc2,g_ether` and whatever comes after it.
5)  Finally, in the boot partition of the microSD card add a file called `ssh`.  If you're using Windows do not let it add a file extension like .txt: the file should *only* be called `ssh`.
6)  Pop the card out of your PC and insert it into your Pi.  Plug a microUSB cabe into the Zero's **data port**  (the one labelled `USB`, not `PWR IN`), or a USB-A to USB-A cable to the 3A+'s USB port, or a USB-C cable into the Pi 4's Type-C power port, and the other end into your PC.  After the board boots (if you're using a Zero remember that it's not the fastest machine around so this could take several minutes on the first boot) open PuTTY on your PC and try SSH into a machine with the IP address `raspberrypi.local`.  You should be able to log in and use the Pi as normal.

#Troubleshooting.

The main reason I wanted to write this article was to document some troubleshooting I've had to go through on occasion.  All of this was done for Windows 7 machines, so may not be relevant for anything else.

**If PuTTY can't find a device with the address `raspberrypi.local`**
- Make sure you've installed Bonjour or the Avahi-daemon (see step 1 of the guide).  This allows you to connect to a device on the local network using the destination machine's hostname rather than the IP address.

**If putty still can't find a device with that name**
Sometimes Windows has problems with USB gadgets. In particular, it seems to sometimes recognise them as the wrong type of device and/or install the wrong driver.  

1)  Go to your Start Menu and type `device manager`.  This should bring up a shortcut for the Device Manager, which you should click on.  This window lists all of the hardware devices on your PC as Windows sees them.  
2)  Click on the expanding arrow beside "Network adapters", and look at the list of network devices which you have.  If the Pi Zero USB gadget is working you should see one called "USB ethernet/ RNDIS Gadget".  
3)  If not, expand the section called "COM PORTS".  One of these may be your Pi, which has been given the wrong identity by your PC.  Try unplugging your Zero and then plugging it back in:  a device should disappear and then reappear.
4)  When you know which device is the Pi, download the appropriate [USB ethernet/RNDIS Gadget driver](http://www.catalog.update.microsoft.com/Search.aspx?q=USB%20RNDIS%20Gadget).
5) In the Device Manager, right click on the Pi Zero and hit "properties".  Go to the Drivers tab, and select "update driver".  When it asks how you want to find the driver, select "browse my computer for driver software", and select the downloaded driver file.  This should assign the correct driver to the Pi Zero, and allow your PC to talk to the Pi correctly.

**A note on power requirements and the 4B**

The 4B is the first model B with an OTG port, and it's also the most power-hungry Pi at the time of release.  The official recommendation is that this board needs a 3A power supply, although this is usualy an over-estimate to allow USB devices to draw power and so on.  I've booted my 4B using my PC monitor's 2.5A charging port.  **However**, the PC complained initially about the device pulling too much current and "malfuncitoning".  I had to tell the PC to ignore this and continue several times, and eventually it worked, but be aware that you'll definitely need a higher current port on your PC to run the 4B, and the PC may complain anyway.  I'm a little concerned that this may stress the USB port's power circuitry, so be careful.
