#Sharing files on the Pi over the network via Samba

This is mostly a copy of the guide provided by MagPi magazine, available [here](https://www.raspberrypi.org/magpi/samba-file-server/).

This walks you through how to set up a folder on the Pi which is shared over the network, so that the contents of the folder are available to other machines on the same network.

1) First, install samba: `sudo apt-get install samba samba-common-bin`

2) Then, make a folder to hold all of your shared code: `mkdir -m 1777 [path/to/folder]`.  The `-m 1777` bit sets the folder permissions.  The `777` allows anyone to read, write or execute files and programs withinn the folder.  You could set this to be a bit more restrictive: check out the help files for `chmod` to see how to do this.  The `1` at the start of the permissions is known as the stick bit.  It ensures that only the owner of the folder can move or delete it, even if other people can move or delete the contents of the folder.  If you want to use a pre-existing folder then use `-chmod 1777 [path/to/folder]` instead.

3) You then need to tell Samba that you want it to share the folder by editing Samba's configuration file.  Run `sudo nano /etc/samba/smb.conf` in a terminal.  This should open a file which already contains a lot of text.  Skip to the bottom and add the following:
```
    [A Shared Folder]             #Give your shared folder a name
    Comment = Pi shared folder    #A comment describing what the folder is for
    Path = /share                 #The path to the folder
    Writeable = Yes               #Can other people write to it?
    create mask = 0777            #What rights do other people have with files created in the folder?
    directory mask = 0777         #What rights do other people have with folders created in the folder?
    Guest ok = yes                #If this is set to `yes` then visitors don't have to be logged in to access the folder.
```
    There are many many more settings which can be tweaked here.  Check out the [samba.conf configuration manual](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html) for more details.  To create more than one chared folder simply copy and paste the configuration text for each folder.

4) Before it can be run Samba needs a password for users.  We won't use this to access out file, but if you ever configured your Samba shares to require people to log in before accessing the files you'd need this password.  Run `sudo smbpasswd -a pi` and follow the prompts.

5) Finally, now that Samba is fully configured and set up, tell it to restart to load the changes: `sudo /etc/init.d/samba restart`

Your folder should now be accessible across your network.  In Windows, Open up My Computer, and in the Network section to the left your Pi should be visible, and your folder should be contained within that section.
