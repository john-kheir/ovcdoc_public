# Bootstrap the Stress Test Cloud Space

The bootstrap script discussed below needs to be performed only once per environment.

It will setup a new cloud space dedicated to testing the environment in which it is setup.

Next to the new cloud space, also a new user and account will be created, both with the same name: system. Furthermore a virtual machine, named `master` will be created, in which a docker container will be started.  

Your first step will be to **connect to any of the CPU nodes** of the environment you are about to stress.

For detailed instructions on how to connect to a cpu node check the [How to Connect to an Environment](connect.md) documentation.

In summary:
- Clone the git repository of your environment to your personal computer, check the [Preparing for indirect access to ovc_git](preparing_for_indirect_access.md) documentation for this
- Connect to ovc_git:
  ```
  ssh $ip-address-of-master-cloud-space$ -l root -A -p 2202 -i /opt/code/git/openvcloudEnvironments/poc/keys/git_root
  ```
- On ovc_git lookup the ip address (instance.ip) of any (here i.e. node 1) of the CPU nodes in `service.hrd` of `/opt/code/git/openvcloudEnvironments/$name-of-your-env$/services/jumpscale__node.ssh__be-conv-2-01`
- From ovc_git connect over ssh, include the -A option:
  ```
  ssh $ip-address-of-the-cpu-node$ -A
  ```

Next, make a directory in the root folder:
````
sudo -s
mkdir performance
cd performance
````

Copy the script on the node:
```
curl https://gist.githubusercontent.com/zaibon/bd1672f762cb3e7f5342/raw/boostrap.py > bootstrap.py
```

Check the copied content of the bootstrap script:
````
cat bootstrap.py
````

Execute the bootstrap script, specifying the environment you are about to stress test, here `du-conv-2`:
```
jspython bootstrap.py --url 'du-conv-2.demo.greenitglobe.com'
```

This script will create a user and account called **system**, create a virtual machine called **master** in the default clouds pace of the system account.  

If you like to check all this in the **Cloud Broker Portal** using the following credentials:
 * **Login**: system  
 * **Password**: gig1234

![](master.png)

The bootstrap script also will install docker and start a docker container with the image https://github.com/Jumpscale/docker_ubuntu1510_python.

Both the docker container and the `master` virtual machine hosting the docker container are accessible over SSH, for which following port forwardings have been configured by the bootstrap script:

![](PortForwardings.png)

In the very end you will be required to enter your "git.aydo.com" user name and password.

When the script is done you should see the following message, inviting you to go to the next step, running the [Setup](setup.md) script:
```
####################################
Bootstrap done !
Now connect to the master node and execute the setup script.
Make sure that you have ssh-agent running and loaded with your keys authorized for github and git.aydo before executing the next commands
to connect to the master docker (password: gig1234):
ssh root@2201 -p 22 -A
then once connected do:
cd /opt/code/git/0-complexity/ays_stresstest/scripts; jspython setup.py --url du-conv-3.demo.greenitglobe.com --login system --password gig1234 --location dev         --cloudspace default --stacks 1,2,3,4
####################################
```
