miscellaneous
=============

## passwordless `sudo`

modify the sudoers file (`$ sudo visudo`) and add the following content:
```bash
# passwordless sudo for your-user for all commands.
your-user ALL=(ALL) NOPASSWD: ALL
```

**note**: this is a lax security policy, and should be implemented judiciously. it's more reasonable to observe the principle of least privilege, which would usually be implemented as only whitelisting the exact commands a given user needs to run with escalated permissions.

## virtualbox

### all ssl certificates are expiring

this is probably due to the vm syncing it's clock with host hardware, and running into problems. time sync is an ongoing virtualization issue. the fix here is to just disable guest sync with the host clock:

```bash
vboxmanage setextradata "vm name" "vboxinternal/devices/vmmdev/0/config/gethosttimedisabled" 1
```
