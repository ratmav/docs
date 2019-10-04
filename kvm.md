kvm
===

[documents](https://www.linux-kvm.org/page/documents)

# installation

## verify the cpu and bios kvm support

```bash
$ sudo egrep -c '(vmx|svm)' /proc/cpuinfo
```

**note**:output should be “1” or greater.

## install packages

```bash
$ sudo yum install -y qemu-kvm libvirt libvirt-python libguestfs-tools virt-install
```

## enable and start `libvirt`

```bash
$ sudo systemctl enable libvirtd && systemctl start libvirtd
```

## verify kernel modules

```bash
$ sudo lsmod | grep kvm
```

**note**: should see results for `kvm` and `kvm_intel` (on intel hardware).

## network bridge

### host-only bridge

**todo**

### bridge to lan

#### bridge ethernet interface

**note**: assumes wired interface's identifier is `em1`. verify the identifier for the interface being bridged via `ip addr show`.

add `bridge=br0` to the end of `/etc/sysconfig/network-scripts/ifcfg-em1`.

#### add bridge to network scripts

add the following to `/etc/sysconfig/network-scripts/ifcfg-br0`:

```bash
device="br0"
bootproto="dhcp"
ipv6init="yes"
ipv6_autoconf="yes"
onboot="yes"
type="bridge"
delay="0"
```

### enable network forwarding

add `net.ipv4.ip_forward = 1` to `/etc/sysctl.conf`, then read the configuration:

```bash
$ sudo sysctl -p /etc/sysctl.conf
```

### restart network manager

```bash
$ sudo systemctl restart networkmanager
```
**note**: for the bridge to be usable by guests, a reboot may be required.

## storage pools

### create pool directories

```bash
$ mkdir /home/deployment-user/kvm_storage
$ sudo virsh pool-build isos # bootable os iso images go here.
$ sudo virsh pool-build images # guest disk images, including "golden" images go here.
```

**note**: any files moved into the `kvm_storage/images` or `kvm_storage/isos` directories will be automagically chwon'd by root.

### define storage pools

```bash
$ sudo virsh pool-define-as isos dir - - - - "/home/deployment-user/kvm_storage/isos"
$ sudo virsh pool-define-as images dir - - - - "/home/deployment-user/kvm_storage/images"
```

### autostart storage pools

```bash
$ sudo virsh pool-autostart isos
$ sudo virsh pool-autostart images
```

### start storage pools

```bash
$ sudo virsh pool-start isos
$ sudo virsh pool-start images
```
