# Docker 

Inspired and heavily copied from [Krijger's docker-cookbooks][docker-cookbooks]
I've created a bunch of docker-images (and the respective `Dockerfiles`) to
work with.

The longterm goal is to combine docker with [Vagrant][vagrant] and [Ansible][ansible]
in order to get to leight-weight, flexible and fast to spin up development
environments.

## Setup your Env

Currently, this is a real pain in the neck! So, I try to recap every single step I've made.

Since I'm working on a Mac I don't have native LXC support on my system. Therefore,
I have to use Linux based VM's to fiddle with Docker. So the first prerequisite to install 
is [VirtualBox][virtualbox] (which is easy - download, install, that's it) - if you don't have
installed already.

After that you're ready to install [Boot2Docker][boot2docker] - a lightweight linux VM 
with a preinstalled docker-server. The easiest way to install it is 
via [Homebrew][homebrew].

	brew update && brew install boot2docker docker
	
So, since the docker-server will run in a Linux-VM whereas the docker-cli is executed 
locally on your Mac they'll have to talk to each other. Therefore, you have to define
a system-variable pointing to your docker-server:

    export DOCKER_HOST=tcp://127.0.0.1:4243

Another, very important point is port-forwarding. Whenever you run a container, this
container is started on top of the virtual linux system - not your Mac! So when
you run docker like:

    docker run -p 22 -d schoeffm/ssh /usr/bin/supervisord -c /etc/supervisor.conf

the exposed port (22 in this example) is exposed to the virtual machine - not your Mac!
So you're not able to connect to this container form your Mac! In order to fix this, you'll have to
define a bunch of port-forwardings for your VM. To do this in one go, execute this 
for-loop

    for i in {49000..49900}; do                                                                                               
        VBoxManage modifyvm "boot2docker-vm" --natpf1 "tcp-port$i,tcp,,$i,,$i";                      
        VBoxManage modifyvm "boot2docker-vm" --natpf1 "udp-port$i,udp,,$i,,$i";                      
    done 

whereas the `boot2docker-vm`-part should match your VM's name (in case you've changed the default).
Just do this once (another execution will complain that the NAT-rule is already defined).

## Several Services and the Container-Hirarchie

### Not only one process
Docker container most often host one process - i.e. a tomcat-server or just a single bash-script 
execution. However, when working with dev-environments it's very likely that you want to execute 
several processes - i.e. a ssh-deamon to log into the container (and check logs) along with 
the actual server-process.

The solution to this problem is already addressed in the [docker-documentation][supervisord]. The missing point is a strategy for all your container to make use of this concept - fortunately this strategy is sketched out in [this blog-post][supervisor-hirarchie].

### Defining a sensible image-hirarchie

So the basic idea is that you prepare a docker-image which sets the ground for managing 
processes using [supervisor][supervisor]. All other images you create will be inherited from that basic
image, building a hirarchie of images where all descendants are capable of hosting several processes.

    schoeffm/supervisord
    └ schoeffm/ssh
    	├── schoeffm/jdk-oracle:7
    	│		└── schoeffm/tomcat:7
		│			└── schoeffm/jenkins
		└── schoeffm/nodejs

So the first child image is one that defines a ssh-deamon - also a basic requirement for my 
dev-environments (since I'd like to look into the processes during development) so this is effectively just
an extension to its parent, representing a further, more usable, base image.

All other images that follows will extend the `schoeffm/ssh`-image.

[supervisor-hirarchie]: http://blog.trifork.com/2014/03/11/using-supervisor-with-docker-to-manage-processes-supporting-image-inheritance/
[supervisord]: http://docs.docker.io/examples/using_supervisord/
[supervisor]: http://supervisord.org
[docker-cookbooks]: https://github.com/Krijger/docker-cookbooks 
[vagrant]: https://vagrantup.com
[ansible]: http://www.ansible.com/home
[boot2docker]: https://github.com/boot2docker/boot2docker
[homebrew]: http://brew.sh
[virtualbox]: https://www.virtualbox.org
