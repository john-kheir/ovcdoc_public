# How to Connect to an Environment

## Introduction

The core of an OpenvCloud environment is the master cloud space, which consist of the following docker containers:
- ovc_git
- ovc_master
- ovc_proxy
- ovc_dcpm

The master cloud space and its containers can run locally, close to the actual OpenvCloud environment it controls or remotely, for instance in another OpenvCloud environment, such as at mothership1.com.

When installed locally, Green IT Globe typically uses the [Shuttle XPC nano NC01U5](http://www.shuttle.eu/products/nano/nc01u5/) Mini-PC.

For each environment managed by Green IT Globe a master git repository is maintained at https://git.aydo.com/ ("AYDO"). The git repository for the environment "be-scale-1" for instance is available at https://git.aydo.com/openvcloudEnvironments/be-scale-1. Access is of course restricted.

On the ovc_git docker container of your environment this git repository is cloned, holding all configuration information, including all the keys in order to access all other docker containers of the master cloud space of your OpenvCloud environment.

So in order to access you OpenvCloud environment you hve two options, get direct or indirect access to the git repository:

- Direct access requires that you know the credentials for opening an SSH connection to ovc_git
- Indirect access requires that have the AYDO credentials in order to first clone the git repository of your environment, in order get to the the keys to access ovc_git and all other docker containers


## Accessing ovc_git indirectly

For the indirect access, there are some prerequisites, all documented [here](preparing_for_indirect_access.md), explaining:
- How to get access to AYDO
- How to configure your git accounts to use your personal SSH keys for authentication
- How to install and configure git on your personal computer

From a well prepared computer, as documented [here](preparing_for_indirect_access.md), your first step will be to clone the repository from AYDO to your local (virtual) machine:
```
git clone https://git.aydo.com/openvcloudEnvironments/be-scale-1
```

If the repository was already cloned previously on your machines, pull the latest update from the directory where it was cloned previously:
```
git pull
```

Make sure the cloned keys file is protected, not accessible to other users, it should be "rw_ --- ---" (600), not "rw- r-- r--" (644):
```
chmod 600 be-scale-1/keys/git_root
```

In order to connect to `ovc_git`, using the git_root identity file (-i) for this environment, in this example for "be-scale-1":
```
ssh be-scale-1.demo.greenitglobe.com -l root -i be-scale-1/keys/git_root

root@ovcgit:~#
```
