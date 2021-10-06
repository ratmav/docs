miscellaneous
=============

## passwordless `sudo`

modify the sudoers file (`$ sudo visudo`) and add the following content:
```bash
# passwordless sudo for your-user for all commands.
your-user ALL=(ALL) NOPASSWD: ALL
```

**note**: this is a lax security policy, and should be implemented judiciously. it's more reasonable to observe the principle of least privilege, which would usually be implemented as only whitelisting the exact commands a given user needs to run with escalated permissions.
