### Cloud Spaces

A cloud space is logical grouping of cloud resources, commonly referred to as a tenant.

Each cloud space is associated with one, and only one account. Per account you can have one or more cloud spaces, that are all associated with the same account.

The **Cloud Spaces** page lists all cloud spaces:

![[]](CloudSpaces.png)

From there you can navigate to the **Cloud Space Details** page of a specific cloud space:

![[]](CloudSpaceDetails.png)

From the **Actions** dropdown on the **Cloud Space Details** page you can choose to:
- Rename the cloud space
- Delete the cloud space
- Create a virtual Machine
- Create a virtual Machine on Stack
- Move, deploy, remove and reset virtual firewall
- Grand and remove user access

For each new cloud space a private network is automatically created, shared by all virtual machines in that cloud space. In the **Details** section of the **Cloud Space Details** page this private network is referenced with its **Network ID**. When you click this **Network ID** you navigate to the **Private Network Details** page for that cloud space:

![[]](PrivateNetworkDetails.png)

On the **Private Cloud Details Page** you also see the **Management IP Address** and the **Public IP Address** for the cloud space.

#### Port Forwardings

In order to make virtual machines on the cloud space publicly accessible you will need to set-up port forwards from the public network to the private network.


#### User Access

In **Users Access** section you see all the users with access to the cloud space:

![[]](UsersAccess.png)

By clicking the **+ Grant User Access** link you can grant other users to the cloud space:

![[]](GrantUserAccess.png)

#### Virtual Machines

In the **Virtual Machines** table all virtual machines running in the cloud space are listed.

![[]](VirtualMachines.png)

In order to create a virtual machine you choose **Create Machine** or **Create Machine on Stack** from the **Actions** dropdown menu, which will pop-up the **Create Machine** dialog:

![[]](CreateMachine.png)
