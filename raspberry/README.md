# RaspberryPi Setup-Cheatsheet

## Copy image to sd-card

Insert the SD-card into your card reader

    diskutil list                                        # in order to identify the disk
    diskutil unmountDisk /dev/disk<# from diskutil>      # unmount the SD-card
    sudo dd bs=1m if=image.img of=/dev/rdisk<# from diskutil>   # copy everything over 
    diskutil unmountDisk /dev/disk<# from diskutil>      # unmound it again

## Adding a user
__Notice__: Since you'll have to type in the users password. Be sure to adjust the keyboard layout before.

- `adduser schoeffm` will create a new user 
- `adduser schoeffm sudo` will add the given user to the sudo-ers list
- `usermod -a -G video schoeffm` in order to be in the `raspistill` group

## Activate SSH (headless setup)

You will need to have your SD card connected to your computer:

Create an empty file in the root of the SD card named `ssh` (without dot or extension).

This will enable ssh on your Raspberry Pi so you will be able to connect next time you plug in the Raspberry Pi. After booting you can connect with:

    ssh pi@raspberrypi

The default-username is `pi` and its default-password is `raspberry`

## Fixing SSH

__Notice__: Be sure you've placed your public key into `~/.ssh/authorized_keys` so you can still login

When starting a fresh installation SSH deamon (normally) is already up and running with default settings. Those settings will allow login using username and password. It's way more safe to restrict login to pubkey and prevent root user to login in via SSH.

Edit `/etc/ssh/sshd_config` in order to change the existing configuration:

- deactivate password authentication (what's left ist pubkey authentication):

        # change to 'no' 
        PasswordAuthentication no  

- disable ssh-access for user root (which means he needs physical access)

        # Authentication:
        LoginGraceTime 120
        PermitRootLogin no
        StrictModes yes

After saving your changes restart the ssh-deamon: 

    sudo service ssh restart

## Secure your SSH-server with fail2ban

    sudo apt-get update
    sudo apt-get install fail2ban

    # edit your configuration as follows
    sudo vi /etc/fail2ban/jail.local
    [ssh]

    enabled = true
    port = ssh
    filter = sshd
    logpath = /var/log/auth.log
    bantime = 900
    banaction = iptables-allports
    findtime = 900
    maxretry = 3

    # retart fail2ban service to make config active
    sudo service fail2ban restart:w

    # watch all banned IPs
    sudo iptables -L -n --line

    # unban a specific IP - number references the line to be removed
    sudo iptables -D fail2ban-ssh 1


## Setting up WiFi

### Measrue Wifi Signal Strengh

In order to list all wifi signals your raspberry receives (and show the signal strength) issue this:

     sudo iwlist wlan0 scan | egrep "Cell|ESSID|Signal|Rates"
     
### Setup Wifi initially (headless)

After setting up the OS-image on the SD-card 

1. insert the SD-card again into your Mac.
2. go to /Volumes/boot

        cd /Volumes/boot

3. Create a file in this directory called `wpa_supplicant.conf`

        touch wpa_supplicant.conf

4. edit that file (like described in the next section)
        
        ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
        network={
            ssid="YOUR_NETWORK_NAME"
            psk="YOUR_PASSWORD"
            key_mgmt=WPA-PSK
        }

5. boot your PI up

### Setup Wifi after you've installed the OS

To setup your WIFI edit the following file: `/etc/wpa_supplicant/wpa_supplicant.conf`

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

#### Create an unsecured ad-hoc network

For outdoor projects you most probably don't have a nearby wifi available - thus, it's hard to connect to the Pi and configure it (i.e. setting up/starting a timelapse session).
One solution is to configure the Pi to open an ad-hoc network (see [this blog-post for detailed explanations](https://spin.atomicobject.com/2013/04/22/raspberry-pi-wireless-communication/))

__Notice__: The network is not secured - everybody can connect to it!! This should only be used for very short configuration periods (where the network is teared down afterwards).

##### Setup an interface

We'll need to configure our WIFI card accordingly. Hence, open `/etc/network/intefaces` and add the followin' block:

    auto wlan0
    iface wlan0 inet static
      address 192.168.50.1
      netmask 255.255.255.0
      wireless-channel 1
      wireless-essid RPiAdHocNetwork
      wireless-mode ad-hoc

Which gives your `wlan0` interface (assuming you have one connected/onboard) a static IP address, a proper ESSID as well as the _ad-hoc_ mode. Now, restart the inteface 
    
    sudo ifdown wlan0
    sudo ifup wlan0

##### Setup a DHCP server

When you try to connect to your Ad-Hoc network you won't get an IP address - for that to work you'll have to provide a working DHCP server as well (which assigns IP addresses to connecting clients).

We'll use `dnsmasq` which is a combined DHCP and DNS server: 

    sudo apt-get update && sudo apt-get install -y dnsmasq 

Open `/etc/dhcpcd.conf` and turn off DHCP processing for `wlan0` (which we assigned a static IP address already):

    denyinterfaces wlan0

__Notice__: Be sure to place this line _above_ all `interface` lines already configured

Now, configure the DHCP server by editing `/etc/dnsmasq.conf`:

    interface=wlan0             # Use interface wlan0  
    listen-address=192.168.50.1 # Explicitly specify the address to listen on  
    bind-interfaces             # Bind to the interface to make ensure we aren't sending things elsewhere  
    server=8.8.8.8              # Forward DNS requests to Google DNS  
    domain-needed               # Don't forward short names  
    bogus-priv                  # Never forward addresses in the non-routed address spaces.  
    dhcp-range=192.168.50.5,192.168.50.150,12h # Assign IP addresses between 192.168.50.5 and 192.168.50.150 with a 12 hour lease time 

That's it - finally restart the `dnsmaq` service

    sudo service dnsmasq restart
    sudo service dnsmasq status

#### Create a secured access point

This solution accomplishes the same but provides a real access point (with WPA2 security - see the [original blog post for details](https://frillip.com/using-your-raspberry-pi-3-as-a-wifi-access-point-with-hostapd/)).

    sudo apt-get update && \
        sudo apt-get install -y iw      # install wireless tools to check card
    iw list                             # it should reveal sth. like ...
    ...
    Supported interface modes:
        * IBSS
        * managed
        * AP                            # nice, this wifi-card is able to act as an AP
        * P2P-client
        * P2P-GO
        * P2P-device
    ...

__Notice__: DHCP is working the same way as with an ad-hoc network - so copy the steps from above to setup a proper DHCP service.

##### Setup an interface

    allow-hotplug wlan0  
    iface wlan0 inet static  
        address 192.168.50.1
        netmask 255.255.255.0
        network 192.168.50.0
        broadcast 192.168.50.255

##### Setup hostapd

Create/edit `/etc/hostadp/hostadp.conf` with the following content:

    # This is the name of the WiFi interface we configured above
    interface=wlan0

    # Use the nl80211 driver with the brcmfmac driver
    driver=nl80211

    # This is the name of the network
    ssid=Pi3-AP

    # Use the 2.4GHz band
    hw_mode=g

    # Use channel 6
    channel=6

    # Enable 802.11n
    ieee80211n=1

    # Enable WMM
    wmm_enabled=1

    # Enable 40MHz channels with 20ns guard interval
    ht_capab=[HT40][SHORT-GI-20][DSSS_CCK-40]

    # Accept all MAC addresses
    macaddr_acl=0

    # Use WPA authentication
    auth_algs=1

    # Require clients to know the network name
    ignore_broadcast_ssid=0

    # Use WPA2
    wpa=2

    # Use a pre-shared key
    wpa_key_mgmt=WPA-PSK

    # The network passphrase
    wpa_passphrase=raspberry

    # Use AES, instead of TKIP
    rsn_pairwise=CCMP

We have to reference this file from our deamon-config: `/etc/default/hostapd`

    DAEMON_CONF="/etc/hostapd/hostapd.conf"

Finally, restart the service:

    sudo service hostapd restart
    sudo service hostapd status


## SSH over USB

This one is useful in case you'd like to setup the Pi headless (so without a display attached). First, after creating a valid SD card image - plug it into your Mac and edit the followin' files in the root of the mounded drive:

    vi config.txt

    # at the very bottom add this line
    dtoverlay=dwc2

The above line will set us up for the next file that we alter

    vi cmdline.txt

    dwc_otg.lpm_enable=0 console=serial0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait modules-load=dwc2,g_ether quiet splash plymouth.ignore-serial-consoles

Parameters in this file are not delimited by new lines or commas, they are delimited by space characters. Add the above key-value-pair `modules-load=dwc2,g_ether` at the exact same position as shown above.

Finally, to make sure there is an SSH-server running add a blank file called `ssh` into the root of the mounted drive. 

Only one of the USB-Ports is cabaple of OTG (On the Go) - this is the inner one! Use it to fire the Pi up.  
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

Be sure that the user which issues the `raspistill` command is in the `video`-group. A typical exception would look like:

    * failed to open vchiq instance

To fix this - issue this command first:

    usermod -a -G video schoeffm

After that you should be able to make pictures

    raspistill -o image.jpg

### Auto
    https://github.com/silvanmelchior/RPi_Cam_Web_Interface
