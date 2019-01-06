This is a guide for changing the hostname of a Raspberry Pi board.  It's more or less lifted from The Pi Hut's guide which you can find [here](https://thepihut.com/blogs/raspberry-pi-tutorials/19668676-renaming-your-raspberry-pi-the-hostname), and kept here for future reference/tweaking.

The Hostname is a name for a machine connected to a network, and helps to distinguish and identify machines in a more human-friendly way: rather than trying to remember which IP address is which machine, you can simply call them "Office PC" and "Laptop".   

The default hostname for Raspberry Pi boards is `raspberrypi`, but this gets confusing if you have several Pi boards running at a time.  For example, I have a Zero running PiHole at all times, and I need to make sure that I'm not editing files on that machine when I'm trying to develop code on my 3A+.  To avoid this, I call the Zero `PiHole` and the 3A+ `3Aplus`, so I can clearly distinguish which is which.

(Wikipedia)[https://en.wikipedia.org/wiki/Hostname] suggests that you can have any hostname as long as it is only made of the characters `a` to `z`, `A` to `Z`, `0` to `9` and `-` (although it can't start with a `-`).

To change the hostname:

1) Log in to your Pi via your preferred method and if necessary open a terminal.  Run the command `hostname` to see what the current hostname is.
2) Run the command `sudo nano /etc/hostname`.  This file only contains a single line, which is the name of your device.  Change this to whatever you like.  Quit using `Ctrl-X` and hit `y` and `Enter` on the way out to save.
3) The hostname is also stored in a second file: run `sudo nano /etc/hosts` to modify this.  At the bottom there should be a line which looks like `127.0.0.1   [current hostname]`.  Modify the current hostname to match what you entered into `/etc/hostname`.
4) The new hostname won't take effect until you reboot the Pi.
