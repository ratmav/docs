virtualbox
==========

## centos guest

**NOTE**: uses [centos stream](https://wiki.centos.org/Manuals/ReleaseNotes/CentOSStream)

### os install

be sure to enable the network interface via the anaconda installer gui.

### post-os install

1. update packages on the guest:
    ```bash
    sudo su -
    dnf update
    shutdown -r now
    ```
2. install guest additions on the guest:
    ```bash
    sudo su -
    mkdir /media/VirtualBoxGuestAdditions
    mount -r /dev/cdrom /media/VirtualBoxGuestAdditions/
    dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    dnf install -y gcc kernel-devel kernel-headers dkms make bzip2 perl
    KERN_DIR=/usr/src/kernels/`uname -r`
    export KERN_DIR
    cd /media/VirtualBoxGuestAdditions/
    ./VBoxLinuxAdditions.run
    exit
    ```
3. shutdown the guest.
4. configure core usage for guest on host via virtualbox ui.
4. configure bridged networking for guest on host via virtualbox ui.
5. boot guest and use virtualbox ui on host to configure shared folder.
6. add guest shell user to `vboxsf` user and logout, then login.
7. create `~/.ssh/authorized_keys` on the guest.
8. use shared folder to copy local public ssh key from host to guest.
9. cat public ssh key from shared folder to `~/.ssh/authorized_keys`.
10. run `chmod -R go= ~/.ssh` on the guest to correct permissions.
11. run `VBoxManage guestproperty get <vm-name> "/VirtualBox/GuestInfo/Net/0/V4/IP" | cut -f2 -d " "` on the host to get the guest's IP.
11. test ssh access from host to guest.
