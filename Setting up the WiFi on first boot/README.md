This guide explains how to set up the WiFi on models of Raspberry Pi with built-in WiFi without having to boot the Pi.  It's useful for setting up "headless" systems (which don't have a monitor), as you can get the device to automatically connect to the WiFi without having to use something like USB mode or a serial cable to modify the files first.  This guide was mostly copied from the guide by [Matt Hawkins](https://www.raspberrypi-spy.co.uk/2017/04/manually-setting-up-pi-wifi-using-wpa_supplicant-conf/), and kept here for future reference/tweaks.

1) Write the latest version of Raspbian/NOOBS to a microSD card in the usual manner.
2) Create a file on the `/boot` partition called `wpa_supplicant.conf`.
3) Edit this file with something like Notepad (*not* Microsoft Word, you need a simple text editor, not a document editor) and paste the following in:

```
country=gb

update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
 scan_ssid=1
 ssid="Name_of_network_to_connect_to"
 psk="Network_password"
}
```

5) Save the file, pop the microSD into your Pi and boot it.  It should automatically update the system with the new details and connect to the network.  Don't forget to create a file called `ssh` in the `/boot` partition if you want to SSH in.

The details of what the file contains are below.  There are _loads_ of other settings to add, too many to detail here, so these are just a core few.  Try running `man wpa_supplicant.conf` for more details.

* `country=` 

This sets the country which the device is operating in, as different countries have different regulations.  This should be the [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) code for your nation, e.g. `gb` for the UK or `us` for the USA.

* `update_config=1` 

Setting this to `1` allows this configuration file to overwrite previous settings.


* `ctrl_interface=/var/run/wpa_supplicant`

I can't quite find a good explaination for what this does: apparently it is for controlling who/what can access the network interface.  Presumably not specifying a group means anyone/thing can access it?


* `network={    }`

This contains the details of the connection, like the network name and password.


* `scan_ssid=1`

This controls how the hardware on your board searches for a WiFi network to connect.  In short, a value of `1` should be able to connect to networks with certain security features enabled, but may be more slow.


* `ssid="Name_of_network_to_connect_to"`

You should enter the name of the network you want to connect to here, exactly as it is set on your router.  If in doubt, check something like a tablet or phone which is also connected.


* `psk="Network_password"`

You should type the password for the network here.
