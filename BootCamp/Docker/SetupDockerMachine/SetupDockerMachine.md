# Setup Docker Machine on a virtual machine

Below we provide a step by step guide on how to set-up a working Drupal environment deployed in two Docker containers, one for Drupal and one for MySQL.

Create a new cloud space, suggested name is 'Drupal'


* Add a new virtual machine to the cloud space, suggested name 'DM-Host', this virtual machine will be used to host docker machine.

* Add following port forwardings for this virtual machine:
 * 7122:22
 * 2376:2376
 
4. Open a SSH session into the new virtual machine:

	```shell
	ssh cloudscalers@<ip-address> -p 7122
	```

* If not already the case, add the hostname of the virtual machine (typically "vm-three\_digit\_number") in `/ets/hosts` to the first line:

	```shell
	sudo vi /etc/hosts
	127.0.0.1 localhost virtual_machine_name
	```

* Create new user that is member of the sudo group in order to allow the new user to use sudo commands

	```shell
	sudo adduser new_user_name
	sudo adduser new_user_name sudo
	```
	
* Logout and login again as new_user_name

* Add your public key (`cat ~/.shh/id_rsa.pub`) to the remote `authorized_keys`:

	```shell
	mkdir ~/.ssh
	vi ~/.ssh/authorized_keys
	```

* In order to meet [the prerequisites of Docker](https://docs.docker.com/engine/installation/linux/ubuntulinux/):

	```shell
	sudo apt-get update
	sudo apt-get install linux-image-extra-$(uname -r)
	```
	
* Enable usage of sudo without password (needed for the next step, installation of docker-machine from local machine)
	```shell
	echo ‘yves ALL=(ALL) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/yves
	sudo chmod 400 /etc/sudoers.d/yves => only the owner (root) will have read access
	```
	
* Back on your local machine, install a docker machine (“dm4drupal") on the new virtual machine:

	```shell
	docker-machine -D create -d generic --generic-ip-address 85.255.197.125 --generic-ssh-port 7122 --	generic-ssh-user yves dm4drupal
	```

* Check that docker machine got installed

	```shell
	docker-machine ls
	```
	
* Make the newly created docker machine current:

	```shell
	eval $(docker-machine env docker-machine4drupal)
	docker-machine ls => a star will appear next to docker-machine4drupal
	```
