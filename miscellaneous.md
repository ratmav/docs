miscellaneous
=============

## operating systems

### *nix

#### passwordless `sudo`

modify the sudoers file (`$ sudo visudo`) and add the following content:
```bash
# passwordless sudo for your-user for all commands.
your-user ALL=(ALL) NOPASSWD: ALL
```

**note**: this is a lax security policy, and should be implemented judiciously. it's more reasonable to observe the principle of least privilege, which would usually be implemented as only whitelisting the exact commands a given user needs to run with escalated permissions.

#### remapping capslock

in the `/etc/default/keyboard` (debian) file, set `XKBOPTIONS`:
    * `="ctrl:swapcaps"` (swaps capslock and ctrl).
    * `="ctrl:nocaps"` (capslock becomes an _additional_ ctrl)

#### avoiding the esc key

`Ctrl-[` is a valid escape sequence in at least some (including vi/vim/nvim) terminal applications.
