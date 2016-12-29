## Lubuntu auf NAND installieren
- [LiveSuite][livesuit] für den Mac downloaden und entpacken
- Distro für den [Desktop][lubunto_desktop] od.[Server][lubunto_server] (empfohlen) runterladen
- LiveSetup öffnen und das Distro-Image darin öffnen.

- (FEL-Taste wieder los lassen und) LiveSuite-Anweisungen folgen (Reformattieren der Partition etc.)
- abwarten ... fertig.

## Nacharbeiten
Danach zunächst 
- `apt-get update; apt-get upgrade` ausführen um package-manager zu aktualisieren

### WLAN aktivieren
__Hinweis__: Solange das LAN-Problem (schleichend lahm) des Netzwerks nicht gelöst ist, sollte das einer der ersten Schritte sein, da alles andere Packete aus dem Netz holen muss, was unerträglich langsam wird.

- Treiber ist zwar bereits vorhanden, aber nicht geladen. Dazu die Datei `
  /etc/modulesi` öffnen und folgende Zeile hinzufügen: 
  `bcmdhd` 
  Beim nächsten Neustart steht der Treiber damit zur Verfügung. Um ihn 
  manuell zu laden gibt man folgendes ein `modprobe bcmdhd`
- `apt-get install wireless-tools wpasupplicant` um die Wireless-Tools zu 
  installieren 
- `/etc/network/interfaces` editieren und folgendes Eintragen

		auto wlan0
		iface wlan0 inet dhcp
			wpa-ssid "HomeNet"
			wpa-psk <SCHLUESSEL>

	Das ist insofern kein Problem, solange im Router die Option 
	eingestellt ist, dass immer die gleiche IP für diese MAC vergeben 
	wird. Sollte das mal nicht mehr passen, dann muss in dieser Datei
	die IP statisch festgelegt werden:

		auto wlan0
		iface wlan0 inet static
			address 192.168.178.22
	        netmask 255.255.255.0
	        gateway 192.168.178.1
	        wpa-ssid "HomeNet"
	        wpa-psk <SCHLUESSEL> 

- Danach den Netzwerk-Service durchstarten `service networking restart`

### Zeitzone ändern
Das is lediglich ein Sym-Link im `/etc`-Verzeichnis.
- `ls /usr/share/zoneinfo` gibt alle verfügbaren aus
- `rm /etc/localtime` um die aktuelle zu löschen
- `ln -s /usr/share/zoneinfo/Europe/Berlin /etc/localtime` um sie neu 
  zu definieren
- `vi /etc/localtime` um diese dann zu editieren

### User hinzufügen
__Hinweis__: Wegen der Passwort-Eingabe sollte das Keyboard-Layout zuvor
eingerichtet sein.

- `adduser schoeffm` und dann den Anweisungne folgen
- `adduser schoeffm sudo` um auch diesen Nutzer in die Sudoers-Gruppe
  mit aufzunehmen. Damit ist dieser User dann SSH-fertig

### SSH Server installieren

- `apt-get install openssh-server` damit wird der Server installiert
- `vi /etc/ssh/sshd_config` um die bestehende Konfiguration zu ändern
  - Passwort-Authentifizierung deaktivieren (damit bleibt nur 
  	die Key-Authentifizierung übrig):

  			# Change to no to disable tunnelled clear text passwords                               │
     		PasswordAuthentication no  

  - Root-User vom SSH-Zugang ausschließen (d.h. der muss physischen Zugang 
  	zur Kiste haben - alles muss via Sudoer-User gehen)

  			# Authentication:
  			LoginGraceTime 120
  			PermitRootLogin no
  			StrictModes yes

### System-Upgrade durchführen

__Vorsicht__: Das Tool warnt einen ohnehin zuvor - aber da ich mir damit bereits eine Installation des Cubietruck zerschossen habe, hier nochmal der Hinweis. Lass es dies via SSH auszuführen - wenn die SSH-Verbindung abkackt während des Upgrades, dann läufst du Gefahr, dass alles hinüber ist.

- `apt-get install python-apt` um danach das Tool `do-release-upgrade` nutzen zu können
- `do-release-upgrade` ausführen

### Spracheinstellungen bzw. Keyboard-Layout ändern
- `sudo apt-get install console-data`
- `sudo dpkg-reconfigure keyboard-configuration` und danach den Bildschirmanweisungen folgen

### Bootparameter ändern
__Hinweis__: Dies habe ich noch nie gebraucht. Falls doch, das is zu tun
- `mount /dev/nanda /mnt` Flash nach mnt mounten
- `vi /mnt/uEnv.txt` in dieser Datei werden allerlei Optionen festgelegt.
- `umount /mnt`
- `snyc`
- `reboot`

### Elasticsearch installieren
__Hinweis__: Um ES nutzen zu können muss man erst Java installieren

- `wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.1.1.deb` zunächst runterladen - auf die Version achten
- `sudo dpkg -i elasticsearch-1.1.1.deb` und schon ist das Teil installiert

### SD-Karte

#### SD Card als Zusatz-Speicher
- `sudo fdisk -l` lists the current partition table (look for something called `mmcblk0`
- `mount -t vfat -ouser,umask=0000 /dev/mmcblk0 /media/card` mounts the sd-card to the 
  given mount-point under `/media/card`
- Besser ist es allerdings, sollte die Karte dauerhaft im Truck bleiben, diese 
  in die `fstab` einzutragen und sie somit automatisch zu mounten 
  `/dev/mmcblk0   /media/card    auto    user,noatime,umask=0000   0   0`

`apt-get clean` immer schön sauber halten

#### SD Card-Backup dauert ewig
Mit dem in allen Raspberry-Foren gezeigten `dd`-Befehl für Mac könnte man schier verzweifeln. 
Mit meiner 16GB SD-Card kann man nicht auf das Ergebnis warten - über eine Stunde pro GB!! 
Stellt sich heraus, dass dies kein Problem ist, das nur ich habe, sondern gemein hin beim 
Mac so ist. Umgehen kann man das Ganze, indem man nicht den Disk-Device dupliziert, sondern 
die Raw-Variante davon (die eine verbesserte R/W-Performance bietet):

	# /dev/rdiskX anstelle von /dev/diskX
	sudo dd bs=1m if=~/2013-10-09.backup.img if=/dev/rdisk3  

#### Image zurückspielen auf die SD-Karte
Wichtigster Punkt beim Zurückspielen eines gesicherten Images ist, dass die Karte nicht gemountet sein darf.

	df -h
	diskutil unmount /dev/disk3s1
	dd bs=1m if=./backup.img of=/dev/rdisk3

- Mount-Punkt und damit die Karte rausfinden
- die Karte aushängen
- das Image kann nun zurückgespielt werden

[livesuit]: http://ubuntuone.com/7GLnElgM41yoGLZfRKxXzk
[lubunto_server]: http://docs.cubieboard.org/tutorials/a20-cubietruck_lubuntu_server_releases 
[lubunto_desktop]: http://docs.cubieboard.org/tutorials/a20-cubietruck_lubuntu_desktop_releases
[dd_on_mac]:http://daoyuan.li/solution-dd-too-slow-on-mac-os-x/
