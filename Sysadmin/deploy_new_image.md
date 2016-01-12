# How to deploy a new OS image

## Clone the repository of your environment

The image templates are under `https://git.aydo.com/openvcloudEnvironments/$name-of-your-env$/tree/master/servicetemplates`

From a well prepared computer, as documented [here](preparing_for_indirect_access.md), your first step will be to clone the repository of your environment from AYDO to your local (virtual) machine:
```
git clone https://git.aydo.com/openvcloudEnvironments/$name-of-your-env$
```

## Create a new image directory

For each image there is a directory under `/opt/code/git/openvcloudEnvironments/$name-of-your-env$/servicetemplates`. In order to create a new image it is best to start from a copy of any of the existing AYS image directories, and name the new directory to your new image.

Below we start from the image directory for Ubuntu 14.04 (64 bit):

```
cd /opt/code/git/openvcloudEnvironments/$name-of-your-env$/servicetemplates
```

Let's use this image:
https://cloud-images.ubuntu.com/wily/current/wily-server-cloudimg-amd64-uefi1.img

```
mkdir image_wily-server
```

```
vi service.hrd
```

```
platform.supported             = 'generic'

web.export.1                   =
    checkmd5:'false',
    dest:'/opt/jumpscale7/var/tmp/templates/willy-server-cloudimg-amd64-uefi1.qcow2',
    source:'/wily/current/wily-server-cloudimg-amd64-uefi1.img',
    url:'https://cloud-images.ubuntu.com',
```

```
vi actions.py
```


```
from JumpScale import j

ActionsBase=j.atyourservice.getActionsBaseClass()

class Actions(ActionsBase):
    """
    process for install
    -------------------
    step1: prepare actions
    step2: check_requirements action
    step3: download files & copy on right location (hrd info is used)
    step4: configure action
    step5: check_uptime_local to see if process stops  (uses timeout $process.stop.timeout)
    step5b: if check uptime was true will do stop action and retry the check_uptime_local check
    step5c: if check uptime was true even after stop will do halt action and retry the check_uptime_local check
    step6: use the info in the hrd to start the application
    step7: do check_uptime_local to see if process starts
    step7b: do monitor_local to see if package healthy installed & running
    step7c: do monitor_remote to see if package healthy installed & running, but this time test is done from central location
    """


    def configure(self, serviceObj):
        from CloudscalerLibcloud.imageutil import registerImage
        name = 'Wily Server 15.10 amd64'
        imagename = 'willy-server-cloudimg-amd64-uefi1.qcow2'
        registerImage(serviceObj, name, imagename, 'Linux', 10)
```

```
vi instance.hrd
```

Each directory contains 3 files:

- **services.hrd** contains the all required information about ftp server that holds your image

  - **url**: address of your ftp server from where the image is available, e.g. `ftp://pub:pub1234@ftp.aydo.com`
  - **source**: exact location on the ftp server from where the image can be downloaded, e.g. `/images/image_windows2012`
  - **checkmd5**: whether the MD5 checksum needs to be checked, typically 'true'
  - **dest**: directory where the image needs to be downloaded, e.g. `/opt/jumpscale7/var/tmp/templates/image_windows2012.qcow2`

- **actions.py** defines the configure method that will be called to register the image once it got downloaded successfully

  - **name** sets the image name, e.g. 'image_windows2012'
  - **imagename** sets the disk image name as physically saved, e.g. 'image_windows2012.qcow2'
  - **registerImage(serviceObj, name, imagename, category, minimum-image-size)** the actual registration method, where you have two additional parameters:
    - **category** specifies under which category the image will be available to the end user, typically 'Linux' or 'Windows'
    - **minimum-image-size** sets the minimum image size, in case of a windows image you have to set it to at least 20, otherwise 10 is fine for linux images

- **instance.hrd** (leave it empty)


## Save, commit and push your changes to the repo

```
git add image_wily-server
git commit -m "new image"
git push
```

Not used:
```
git config --global push.default simple
```

## Install the image

Open an SSH session on the ovc_git machine.

OLD/DON'T USE:
Update the AYS metadata on ovc_git, which will fetch the newly created the HRD files:
```
ays mdupdate
```

Instead, manually update the repo:
```
git pull
```


Go to the cloned environment repo on ovc_git, for instance for environment 'du-conv-2':
```
cd /opt/code/git/openvcloudEnvironments/$name-of-your-env$/servicetemplates
```

And finally install the image, make sure to specify the name of node:
```
ays install -n image_wily-server --targettype node.ssh --targetname $name-of-a-node-in-your-env$
```

(ays install -n image_wily-server --targettype node.ssh --targetname be-scale-1-01)

OLD, skip:
## Sync your image

In the **Cloud Broker Portal** go to the **Location Details** page for your location, and choose **Sync Available Images to Cloud Broker** from the **Actions** menu:

![](SyncAvailableImages.png)

Note: if this is your first image, you will as well need to choose the *Sync Available Sizes to Cloud Broker***


## Set image availability

In the **Cloud Broker Portal** go to the **Image Details** page for your newly added image and select **Image Availability** from the **Actions** menu:

![](ImageAvailability.png)

Confirm on which nodes you want to make this image available:

![](ImageAvailability2.png)
