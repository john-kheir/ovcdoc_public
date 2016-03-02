# Installation of the OVC Nodes


## Have your node ready for PXE Install (aka 911)

1. Refer to [Creating a PXE boot environment](https://git.aydo.com/0-complexity/OpenvCloud3/tree/master#README) in order to be able to get a physical machine in '911'-mode.  
This mode gets a system in an OS-booted state where the whole OS is loaded into memory (nowadays being more than plentyful enough), where all tools are available for install/recovery purposes.  
Nowadays (beginning of '16) it is still based on Ubuntu-15.04, but that is likely to change in the near future.   
First step is to (re)boot your node into recovery mode. See [this link](https://git.aydo.com/openvcloudEnvironments/IP_Layout_DEMO/blob/master/Table.md) for the IP address. You might need to request access to this git repository.

2. in `$PXEPATH/pxeboot/tftpboot/pxelinux.cfg/` , symlink the file `911boot` to `01-ma-ca-dd-re-ss-ss`.  
If you have the BIOS of the Physical machine correctly set-up, the machine will load the NIX pxe code, request an IP, tftp the necessary files, and boot these instead of booting from local disk.
You should now be in the recovery (911) OS.

Connect the machine using SSH (default credential are: `root/rooter`).

To be sure, the prompt should be: `root@Installer`

**Note:** If, for what ever reason, you cannot find the gateway to the environment, you can also use the IPMI of the environment:

1. Reboot the node from the IPMI console
2. Chose 911 as the boot image from PXE menu
3. Credentials are `root/rooter`
4. Go to the tools directory: `/root/tools/`
5. Run the following on each node of the environment

```
./Install
```

The installer will use the hostname the machine received to deduct the environment name, so make sure your `$PXEPATH/conf/*` files are correctly edited to your liking.


## Setup the installed OS image

When the image is installed, connect to the node using SSH (default credential are: `gig/rooter`, you can `sudo -s` when logged in to be root).
There is a script which takes care of all the needed stuff to setup the node hardware, to apply it, just run (in root):
```
reboot
```

Wait for the machine to reboot.

Once rebooted connect again to the CPU node and fetch the node.sh script from AYDO:

Unfortunately you can not simply curl this script into your environment yet because of the authentication, so navigate to the link, then copy and paste (vi) the content into your node:
```
curl https://github.com/0-complexity/openvcloud/raw/master/scripts/install/node.sh > /tmp/node.sh
. /tmp/node.sh
```

You might need to enter your git.aydo.com credential on the url.

This will:
- Upgrading Ubuntu system
- Detecting ssd used, building the partition schema
- Creation and initializing ovs partitions
- Allow root ssh connection (root/rooter)
- Ensure that backplane1 is up
- Pre-install lots of ovs dependancies

When this script is done, connect the node to the ovc_git to make the reverse tunnel up:


## Connect node to ovc_git

**DO NOT RUN THIS SCRIPT IN PARALLEL IN CASE OF A REMOTE (not Docker) NODE**

Before running the next scripts (on each of the nodes) please make sure that you have the version tag environment variables set, specifying the versions of JS, AYS and OVC:
```
export JSBRANCH="7.0.2a"; export AYSBRANCH="7.0.2a" ; export OVCBRANCH="2.0.2a" ;

```
```
curl https://github.com/0-complexity/openvcloud/raw/master/scripts/install/pre-install.sh > /tmp/pre-install.sh
bash /tmp/pre-install.sh $REMOTEHOST
```

(Note, name script is legacy, so no real pre-install, but just connect the node to ovc_git)

`$REMOTEHOST` must be the remote ovc_git hostname (or ip), eg: be-scale-1.demo.greenitglobe.com

(In case docker containers you have to specify the ip address of the Shuttle hosting the "master cloud space")
(In case the master cloud space is hosted at mothership1 you have to specify the cloud space ip address)

When the script is done, node should be ready.


### Note (@todo:Move to trouble section)

The following scripts depend on the service.hrd files located at ```/opt/code/git/openvcloudEnvironments/$ENVIRONMENT/services/```

So, if you are re-using a repo, make sure that your reflector and other ip addresses are correct and are in place, or else you will not be able to connect to the tunnels.


## Automated script for the setup of Open vStorage and all dependancies...

On ovc_git, there is a script which will install everything needed on nodes for Open vStorage, etc.

After having run the pre-install script, go to the folder which contains your environment repository (example):
```bash
cd /opt/code/git/openvcloudEnvironments/be-dev-1/
```

From here you have two options:
- Use the « auto-detection setup (highly recommended)
- Or use the the « custom/manual setup »


### Auto-detection setup
This is the simplest way to setup a basic OVS environment if you follow default purpose. Just run this script as many time you have nodes:
```
jspython scripts/cpunode-setup.py
...
```

This auto-detection mode will:
- Search for an available node (ordered by name)
- Search if a ovs-master is already installed by the script
- If not, it will install the master ovs on the node-id you give, then save it
- If yes, it will install the slave node using the master already installed

**Note:** this method will auto-detect master/slave for you. If you want a more custom method, please use the next point.
**Note:** After the first node installation is done , you should have all the ovs and ovc packages downloaded into `/var/cache/apt/archives/*dep` , if you copied the packages in here to the other nodes , you shall cut the download time out of the installation .. you can do this with the following command

```bash
rsync -avzp --progress /var/cache/apt/archives/ root@IP_OF_THE_OTHERNODE://var/cache/apt/archives/
```
do this for all the nodes , note that you can use the internal ips of the nodes , which can be found in [here](https://git.aydo.com/openvcloudEnvironments/IP_Layout_DEMO/blob/master/Table.md)


### Custom setup

You can install the ovs-stuff using the same script as the auto-detect, but with some argument to make custom choice:
```
jspython scripts/cpunode-setup.py --node be-scale-1-01 --master
jspython scripts/cpunode-setup.py --node $XX --slave $IPMASTER
```

The node `$XX` should be replaced by node name (eg: envname-05, ...) and the `$IPMASTER` is the backplane master ip of the master node. The master install script should give you the IP address, it should be like: `10.xxx.1.11`

After that, your node is ready.

Next...
