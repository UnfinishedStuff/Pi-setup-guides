# Passwordless-SSH-on-a-Raspberry-Pi
Set up passwordless SSH for connecting to your Raspberry Pi

This is a modified version of the tutorial published by Alex Eames at [RasPi.TV](http://raspi.tv/2012/how-to-set-up-keys-and-disable-password-login-for-ssh-on-your-raspberry-pi), modified for Putty (he uses BitVise tunneler), and also stored on my GitHub page because I can never find these tutorials the second time around.

The purpose of this guide is to explain how to set up passwordless SSH for accessing your Pi.  You can remotely control a Pi through SSH, where you connect with an SSH client, log in and then gain access to a terminal on the Pi.  However, humans like to use easily remembered (and therefore quite simple) passwords, while secure passwords tend to be far too large and complex to remember.  To remedy this, you can use passwordless SSH.  This involves generating a pair of public and private cryptography keys, and giving the Pi the public section of the key.  When you try to log in the Pi and your computer will compare the keys to make sure you are who you say you are, rather than asking for a password.  This means that you don't have to remember a password and also means that there is no password to hack: as long a you keep the private section of your key safe, the Pi will be secure.

1)  Download both PuTTY and PuTTYGen from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).  PuTTY is the SSH client we'll use to connnect to the Pi, PuTTYGen is the program for creating the keys.
2)  Run PuTTYGen.  On the UI which opens, there should be a button labelled "Generate" in the Actions section.  Press this, and it will ask you to move your mouse around the blank area to create randomness.  After a few moments of this it will generate keys and open a new page of the UI.
3)  The larger window at the top named "Public key for pasting into OpenSSH authorized_keys file" is the key which needs to be transferred to the Pi.  

<div style="text-align:center"><img src ="https://github.com/Shoe-Pi/Pi-setup-guides/blob/master/Passwordless_ssh/1.jpg" /></div>

4)  SSH into your pi using PuTTY: open the program, enter your Pi's I.P. address into the "Host name or IP address" box and press "open".  If you don't know your Pi's I.P. address you may need to log in to your router and find the page where it lists that.  PuTTY should ask you to log in to your Pi using your username and password before opening a terminal where you can type commands: use the default Pi login details, or if you've changed them, your new details.  Type `cd` and hit Enter to go to your home directory, then type `mkdir .ssh` and hit enter to create a directory called `.ssh`.  Finally, type `cd .ssh` and hit Enter to open that directory.
5)  Type `nano authorized_keys` and hit Enter to create and open a file called `authorized_keys`.  **Note the American spelling of authorized: this won't work with the UK spelling.**  Copy the Public key from PuTTYGen and paste it into `authorized_keys` on the Pi.  Save and close the file with `Ctrl-X` and `Y`.
6) Apparently, the permissions need to be changed on `authorized_keys`.  Run `chmod 700 ~/.ssh` to change the permissions on the `.ssh` directory, and `chmod 600 ~/.ssh/authorized_keys` to change permissions on the `authorized_keys` file.  Both of these prevent other people from accessing the file (and so copying or editing your keys) and gives your own account various rights to them.  This should complete the setup on the Pi's end.
7) In PuTTYGen, hit the "Save private key" button.  It may prompt you for a password, it's up to you whether or not you do this.  Save the private key file somewhere you will remember. 
8)  Open Putty, and in the branching menu on the left go to Connection > SSH > Auth.  In the `Authentication parameters` section, hit "Browse" beside the "Private key file for authentication" box, and choose your Private Key file.
9)  In the branching menu at the left, go to Connection > Data.  There's a section called Auto-login name: type your username here (the default is `pi`), so that PuTTY automatically logs you in as this user.
10)  In the menu at the left, go back to Session.  Type the I.P. address of the Pi in the "Host name (or IP address)" box on the right, and then in the box under "Saved Sessions" type a name for this setup and hit the "Save" button just to the right.  This will save all of this information as a profile which you can then load in future so that you don't have to set this up every time.
11)  Finally, start the connection with the `Open` button at the bottom.  It should not ask you for the username to log in with, and will authenticate you with the Private Key.  You're now logging in with your Private key, and no longer need to use a password.

For security reasons, once this is set up it's a good idea to disable password authentication.  **Make sure you check that passwordless authentication is working BEFORE you do this, otherwise you'll be locked out of SSH access!**  It can be quite difficult to undo this if you lose the ssh key file.

12)  On the Pi, run `sudo nano /etc/ssh/sshd_config` in the terminal.  Somewhere in that file is the line `#PasswordAuthentication yes`.  Uncomment it and change it to `no`, so that it looks like this: `PasswordAuthentication no`.  Save and close the file.


