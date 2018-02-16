# How to setup a Cubietruck with Docker-support

The [cubietruck][cubietruck] is the current host of my [nextcloud][nextcloud_docker]-setup - if everything went fine you should be able to open it under [https://schoeffm.ddnss.de](https://schoeffm.ddnss.de).

During the setup of my [RaspberryPi Kubernetes-Cluster][rpicluster_repo] I was forced to recruite my cubietruck as a host for common components (i.e. a Docker registry, Grafana- and Prometheus server etc.). But because of its different form factor I wanted to replace it with an additional Raspberry Pi 3 (which seemlessly integrates into my mini-rack) - so I had to do a migration of my docker based [nextcloud setup][nextcloud_docker] onto cubietruck.

In order to run docker (and containers) on cubietruck I had to install a suitable OS. First, download and flush the [ambian OS][ambian_download] onto a proper SD-card. Next - while connected to a display - fire the machine up, login as `root/1234` and subsequently change that password, create a dedicated user (this one will automatically be in the sudo-er list). 
After that you'll be able to setup the onboard WiFi, connect to the internet, download and install docker:

	$ nmtui-connect <SSID>						# opens a simply config-dialog

    $ curl  https://get.docker.com | sh			# will install docker on the machine
	$ sudo usermod -aG docker <YOUR_USERNAME> 	# log out and back to take effect

That's it for the time ...

__Notice:__ Adjust the SSHd settings as well so nobody but you can actually login.


[rpicluster_repo]:https://bitbucket.org/schoeffm/rpi-cluster
[nextcloud_docker]:https://hub.docker.com/r/schoeffm/rpi-nextcloud/
[cubietruck]:http://cubieboard.org/tag/cubietruck/
[ambian]:http://www.armbian.com/
[ambian_download]:https://dl.armbian.com/cubietruck/Debian_stretch_next.7z
