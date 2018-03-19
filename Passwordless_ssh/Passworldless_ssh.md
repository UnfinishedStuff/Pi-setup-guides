# Passwordless-SSH-on-a-Raspberry-Pi
Set up passwordless SSH for connecting to your Raspberry Pi

This is a modified version of the tutorial published by [RasPi.TV](http://raspi.tv/2012/how-to-set-up-keys-and-disable-password-login-for-ssh-on-your-raspberry-pi), modified for Putty (He uses BitVise tunneler), and also stored on my GitHub page because I can never find these tutorials the second time around.

1)  Download both PuTTY and PuTTYGen from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).  PuTTY is the SSH client we'll use to connnect to the Pi, PuTTYGen is the program for creating the keys.
2)  Run PuTTYGen.  On the UI which opens, there should be a button labelled "Generate" in the Actions section.  Press this, and it will ask you to move your mouse around the blank area to create randomness.  After a few moments of this it will generate keys and open a new page of the UI.
3)  The larger window at the top named "Public key forpasting into OpenSSH authorizzed_keys file" is the key which needs to be transferred to the Pi.  
4)  SSH into your pi, and type `cd ~` to go to your home directory.  Type `mkdir .ssh` to create a folder called `.ssh`, and then `cd .ssh` to enter that folder.
5)  Type `nano authorized_keys` to create and open a file called authorized_keys.  **Note the American spelling of authorized: this won't work with the UK spelling.**  Copy the Public key from PuTTYGen and paste it into `authorized_keys` on the Pi.  Save and close the file with `Ctrl-X` and `Y`.
6) Apparently, permissions need to be changed (I'm not 100% clear on why yet).  Run `chmod 700 ~/.ssh/` to change the permissions for the `.ssh` directory, and `chmod 600 ~/.ssh/authorized_keys` to change permissions on the `authorized_keys` file.  This should complete the setup on the Pi's end.
7) In PuTTYGen, hit the "Save private key" button.  It may prompt you for a password, it's up to you whether or not you do this.  Save the file somewhere you remember. 
8)  Open Putty, and in the branching menu on the left go to Connection > SSH > Auth.  In the `Authentication parameters` section, hit "Browse" beside the "Private key file for authentication" box, and choose your Private Key file.
9)  In the branching menu at the left, go back to Session.  Type the I.P. address of the Pi in the "Host name (or IP address" box on the right, and then start the connection with the `Open` button at the bottom.  It will ask you for the username to log in with, but instead of asking for a password will authenticate you with the Private Key.

For security reasons, once this is set up it's a good idea to disable password authentication.  **Make sure you check that passwordless authentication is working BEFORE you do this, otherwise you'll be locked out of SSH access!**

1)  On the Pi, run `sudo nano /etc/ssh/sshd_config` in the terminal.  Somewhere in that file is the line `#PasswordAuthentication yes`.  Uncomment it and change it to `no`, so that it looks like this: `PasswordAuthentication no`.  Save and close the file.
