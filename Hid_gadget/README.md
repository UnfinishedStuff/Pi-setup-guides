This is a guide for setting up a Pi Zero as a USB keyboard.  It should work on a Pi 3A as well, but I haven't tried it so far.  This is a work in progress.  Once complete, scripts on the Pi should be able to send keyboard strokes to a PC.

It's drawn from two sources:  [Aidan Woods' guide](https://www.aidanwoods.com/blog/building-a-wifi-enabled-usb-rubber-ducky/) is the main basis of the tutorial.  When I tried this it worked perfectly with my Win10 machine but not my Win7 machine.  To get it working on Win7 I had to incorporate some work from [Darell Tan's guide](http://irq5.io/2016/12/22/raspberry-pi-zero-as-multiple-usb-gadgets/).

# Setup

1) Download and install Raspbian as usual.  I've done this with Raspbian Stretch 2018-11-13.
2) Set up WiFi and SSH as described [here](https://github.com/Shoe-Pi/Pi-setup-guides/tree/master/Setting%20up%20the%20WiFi%20on%20first%20boot).
3) Open `/boot/config.txt` and add `dtoverlay=dwc2` to the bottom of it.  (I used nano for this.)
4) Open `/etc/modules` and add `dwc2` to the bottom of the file.
5) Now that the necessary modules are loaded, we need to create a script.  This creates the USB device every time the machine boots, because unfortunately it's not a persistent process.  Create a text file in `/home/pi/` called `hid.sh`.  Paste the following into it:

```
#!/bin/bash
# Snippet from https://github.com/girst/hardpass-sendHID/blob/master/README.md . In which, the following notice was left:
# this is a stripped down version of https://github.com/ckuethe/usbarmory/wiki/USB-Gadgets - I don't claim any rights

modprobe libcomposite
cd /sys/kernel/config/usb_gadget/
mkdir -p g1
cd g1
echo 0x1d6b > idVendor # Linux Foundation
echo 0x0104 > idProduct # Multifunction Composite Gadget
echo 0x0100 > bcdDevice # v1.0.0
echo 0x0200 > bcdUSB # USB2

echo 0xEF > bDeviceClass
echo 0x02 > bDeviceSubClass
echo 0x01 > bDeviceProtocol

echo 1       > /os_desc/use
echo 0xcd    > /os_desc/b_vendor_code
echo MSFT100 > /os_desc/qw_sign
 
mkdir /functions/rndis.usb0
echo RNDIS   > /functions/rndis.usb0/os_desc/interface.rndis/compatible_id
echo 5162001 > /functions/rndis.usb0/os_desc/interface.rndis/sub_compatible_id
 
ln -s ${g}/configs/c.1 ${g}/os_desc


mkdir -p strings/0x409
echo "deadbeef01234567890" > strings/0x409/serialnumber
echo "example.com" > strings/0x409/manufacturer
echo "Generic USB Keyboard" > strings/0x409/product
N="usb0"
mkdir -p functions/hid.$N
echo 1 > functions/hid.usb0/protocol
echo 1 > functions/hid.usb0/subclass
echo 8 > functions/hid.usb0/report_length
echo -ne \\x05\\x01\\x09\\x06\\xa1\\x01\\x05\\x07\\x19\\xe0\\x29\\xe7\\x15\\x00\\x25\\x01\\x75\\x01\\x95\\x08\\x81\\x02\\x95\\x01\\x75\\x08\\x81\\x03\\x95\\x05\\x75\\x01\\x05\\x08\\x19\\x01\\x29\\x05\\x91\\x02\\x95\\x01\\x75\\x03\\x91\\x03\\x95\\x06\\x75\\x08\\x15\\x00\\x25\\x65\\x05\\x07\\x19\\x00\\x29\\x65\\x81\\x00\\xc0 > functions/hid.usb0/report_desc
C=1
mkdir -p configs/c.$C/strings/0x409
echo "Config $C: ECM network" > configs/c.$C/strings/0x409/configuration 
echo 250 > configs/c.$C/MaxPower 
ln -s functions/hid.$N configs/c.$C/
ls /sys/class/udc > UDC
```

This has been lifted mostly from Aidan Woods' guide with some lines from Darell Tan's troubleshooting steps to make it play nicely with Win7.

6) Now that the setup script is in place, we need to make it automatically run on boot.  Modufy `/etc/rc.local` (you may need root rights to do this) and add `sudo /home/pi/Hid.sh`.  On another line, add `sudo chmod -R 777 /dev/hidg0`.  These must go before the line which says `exit 0`.
7) Now if you reboot the Pi and go to `/dev` and run `ls` there should be a device called `hidg0`.

This creates the virtual USB device, and allows you to send keystrokes to the attached PC by writing to the `/dev/hidg0` device.  Aidan Wood's script describes how to do that with C, I'll update this when I've experimented a little with Python.  
