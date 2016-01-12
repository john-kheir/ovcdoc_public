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
cd openvcloud_ays
cp -rf image_ubuntu-1404-x64 image_windows2012
cd image_windows2012
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
git commit
git push
```

## Install the image

Open an SSH session on the ovc_git machine.

Update the AYS metadata on ovc_git, which will fetch the newly created the HRD files:
```
ays mdupdate
```

@todo we need to review the git repo address, needs to be specific per partner/customer

Go to the cloned environment repo on ovc_git, for instance for environment 'du-conv-2':
```
cd /opt/code/git/openvcloudEnvironments/du-conv-2
```

And finally install the image:
```
ays install -n image_windows2012 --targettype node.ssh --targetname du-conv-2-02
```


## Sync your image

In the **Cloud Broker Portal** go to the **Location Details** page for your location, and choose **Sync Available Images to Cloud Broker** from the **Actions** menu:

![](SyncAvailableImages.png)

Note: if this is your first image, you will as well need to choose the *Sync Available Sizes to Cloud Broker***


## Set image availability

In the **Cloud Broker Portal** go to the **Image Details** page for your newly added image and select **Image Availability** from the **Actions** menu:

![](ImageAvailability.png)

Confirm on which nodes you want to make this image available:

![](ImageAvailability2.png)
