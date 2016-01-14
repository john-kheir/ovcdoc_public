# Setup the Stress Test Virtual Machines

Also this step needs to be performed only once per environment.

Make sure to have first setup the stress test cloud space, using the [Bootstrap](bootstrap.md) script.

The setup script is responsible for the creation of all the virtual machines needed for the tests. On each node a virtual machine will be deployed, including the application needed for the performance test.

The setup script needs to be run from the docker container hosted in the master virtual machine that got installed by the bootstrap script.

Before connecting to the docker container, let's make sure `ssh-agent` is running, and that your SSH keys are added:
```
ssh-add -l
```

If not:
```
eval $(ssh-agent -s)
ssh-add $HOME/.ssh/id_rsa
```

Also make sure that your SHH public key is registered at both git.aydo.com and www.github.com.

In order to connect to the docker container, you will need the IP address of the master virtual machine, hosting the docker container, which can be found in the **Cloud Broker Portal** on the **Virtual Machine Details** page for your `master` virtual machine, where you also see the port forwardings setup by the bootstrap script:

![](PortForwardings.png)

Let's now connect, forwarding your current authentication agent connection by using the -A option:
```
ssh root@$ip-address-of-ovc_master$ -p 2201 -A
```

The password for root is gig1234.

The -A option is critical in order for the setup script to work. It enables forwarding of the authentication agent connection. Of course this requires that the ssh authentication agent (ssh-agent) is running in the first place. Moreover your public key needs to be registered on both registered github.com and git.aydo.

Now your are ready to run the actual installation script. The below command is for the environment *`du-conv-3.demo.greenitglobe.com`* on location *`dev`*, having 4 nodes (stacks):
```
cd /opt/code/git/0-complexity/ays_stresstest/scripts
jspython setup.py --url du-conv-2.demo.greenitglobe.com --login system --password gig1234 --location du-conv-2 --cloudspace default --stacks 1,2,3,4
```

Again, remember to adapt the command to your environment:
- Replacing *'du-conv-2.demo.greenitglobe.com'* with the url of the environment you want to perform the test on.
- Replace *'du-conv-2'* with the actual location name of the location, which can be looked up in the **Cloud Broker Portal** under **Locations**.
- Add the total number of nodes after *'stacks'*  

DON'T DO THIS, SINCE IT WILL ALSO DELETE THE SETUP.PY SCRIPT
In case you have to re-run the setup.py script, you first need to do some cleanup:
```
rm -r /opt/code/git/0-complexity/ays_stresstest/*
```

If everything went well a number of virtual machines will have been created:

- **Influxdb** containing a docker container of this image https://github.com/Jumpscale/docker_influxdb and a docker container of this image https://github.com/Jumpscale/docker_redis
- **Mongodb** containing a docker container of this image https://github.com/Jumpscale/docker_mongo
- For each node specified in the setup script with the option --stacks a virtual machine generating the actual stress test workloads

In the **Cloud Broker Portal** you should see following setup:

![](StressTestEnv.png)
