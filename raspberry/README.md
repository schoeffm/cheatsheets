# RaspberryPi Setup-Cheatsheet

## Copy image to sd-card

Insert the sd-card into your card reader

   diskutil list                                        # in order to identify the disk
   diskutil unmountDisk /dev/disk<# from diskutil>      # unmount the sd-card
   sudo dd bs=1m if=image.img of=/dev/rdisk<# from diskutil>
   diskutil unmountDisk /dev/disk<# from diskutil>      # unmound it again

## Adding a user
__Notice__: Since you'll have to type in the users password. Be sure to adjust the keyboard layout before.

- `adduser schoeffm`: will create a new user 
- `adduser schoeffm sudo` will add the given user to the sudo-ers list

## Fixing SSH

__Notice__: Be sure you've placed your public key into `.ssh/authorized_keys` so you can still login

When starting a fresh installation SSH deamon (normally) is already up and running with default settings. Those settings will allow login using username and password. It's way more safe to restrict login to pubkey and prevent root user to login in via SSH.

Edit `/etc/ssh/sshd_config` in order to change the existing configuration:

- Passwort-Authentifizierung deaktivieren (damit bleibt nur die Key-Authentifizierung übrig):

        # change to 'no' 
        PasswordAuthentication no  

- disable ssh-access for user root (which means he needs physical access)

        # Authentication:
        LoginGraceTime 120
        PermitRootLogin no
        StrictModes yes

## Setting up WiFi

To setup your wifi edit the following file:

    sudo vi /etc/wpa_supplicant/wpa_supplicant.conf

Keep the leading lines and only add a proper `network` block.

    country=GB
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1

    network={
        ssid="Network_Name"
        psk="Your_passWord"
        id_str="home"
    }        

At this point, `wpa-supplicant` will normally notice a change has occurred within a few seconds, and it will try and connect to the network. If it does not, restart the interface with `sudo wpa_cli reconfigure`.

__Notice__: You can add several `network`-blocks into that configuration with different `id_str` (i.e. one for home - one for work etc.)

## SSH over USB

This one is useful in case you'd like to setup the Pi headless (so without a display attached). First, after creating a valid SD card image - plug it into your Mac and edit the followin' files in the root of the mounded drive:

    vi config.txt

    # at the very bottom add this line
    dtoverlay=dwc2

The above line will set us up for the next file that we alter

    vi cmdline.txt

    modules-load=dwc2,g_ether

Parameters in this file are not delimited by new lines or commas, they are delimited by space characters. Add the above key-value-pair at the end of that file.

Finally, to make sure there is an SSH-server running add a blank file called `ssh` into the root of the mounted drive. 

That's it. After starting the pi you should be able to connect to it via:

    ssh <user>@<hostname>.local

    # i.e. using default-settings of a fresh installation
    ssh pi@raspberrypi.local

    # or using an adjusted setup
    ssh schoeffm@zeropi.local

## Camera Module

### Turn off LED
    
    # open that file
    vi /boot/config.txt

    # append/replace the followin' entry - turns LED off
    disable_camera_led=1

### Taking pictures

    raspistill -o image.jpg

## Setting up AccessPoint-mode

    sudo apt-get update && sudo apt-get install -y iw       # install wireless tools
    iw list                                                 # should contain sth. like ...
        
    ...
    Supported interface modes:
        * IBSS
        * managed
        * AP                                                # nice, this wifi-card is able to act as an AP
        * P2P-client
        * P2P-GO
        * P2P-device
    ...
