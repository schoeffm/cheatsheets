
## Copy Image-File on SD Card

- first, unmount the device in order

        $ diskutil list
        ...
        /dev/disk4                                                                     
          #:                       TYPE NAME                    SIZE       IDENTIFIER
          0:     FDisk_partition_scheme                        *15.8 GB    disk4     
          1:             Windows_FAT_32 NO NAME                 94.4 MB    disk4s1  
          2:                      Linux                         1.8 GB     disk4s5
          
        $ diskutil unmountDisk /dev/disk4
        
- now copy the image over - use the raw-mode in order to speed things up

		$ dd bs=1m if=ts-image.img of=/dev/rdisk4

## connect via WIFI

- first, create a profile by following the wifi-wizard by:

        $ wifi-menu

- next, the profile which was created by the wizard should be enabled at startup

        $ netctl enable wireless-home

## User-Management

- you'll have to create a non-root user (in order to cut the root user off) and set its password

        $ useradd -m -G users -s /bin/bash schoeffm
        $ passwd schoeffm

- sudo is not instaled by default - so you'll have to instal it

        $ pacman -S sudo

  this will also create a `/etc/sudoers`-File which contains who is allowed to do what as su

- when editing the `/etc/sudoers`-File always use the dedicated `visudo`-command (it checks the syntax)

        $ visudo

## Docker on Arch-Linux

- well, first you'll have to install it - and in order to execute docker as ordinary user, add your user to the docker-group

        $ pacman -S docker
        $ gpasswd -a user docker
        

