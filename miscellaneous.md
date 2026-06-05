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

system-wide approach - in the `/etc/default/keyboard` (debian) file, set `XKBOPTIONS`:
    * `="ctrl:swapcaps"` (swaps capslock and ctrl).
    * `="ctrl:nocaps"` (capslock becomes an _additional_ ctrl)

xfce/x session approach - use `setxkbmap`:
    * `setxkbmap -option ctrl:swapcaps` (swaps capslock and ctrl)
    * `setxkbmap -option ctrl:nocaps` (capslock becomes an additional ctrl)
    * to clear all options first: `setxkbmap -option`
    * to make persistent across reboots:
        * add to xfce session startup: Settings → Session and Startup → Application Autostart
        * or add to `~/.xprofile`

#### avoiding the esc key

`Ctrl-[` is a valid escape sequence in at least some (including vi/vim/nvim) terminal applications.

### popos

#### gpg-verified download

```bash
#!/usr/bin/env bash

check() {
  local key="204DD8AEC33A7AFF"

  gpg --keyserver keyserver.ubuntu.com --recv-keys $key
  gpg --verify SHA256SUMS.gpg SHA256SUMS
  sha256sum -c SHA256SUMS
}

clean() {
  rm -f ./SHA256SUMS*
}

# see download links on https://system76.com/pop/download/.
download() {
  local iso_url="$1"
  local iso="${iso_url##*/}"
  local base="${iso_url%/*}"

  clean
  rm -f "./$iso"

  wget --progress=bar "$iso_url"
  wget --progress=bar "$base/SHA256SUMS"
  wget --progress=bar "$base/SHA256SUMS.gpg"
}

main() {
  local iso_url="$1"

  if [[ -z "$iso_url" ]]; then
    echo "usage: $0 <iso-url>" >&2
    exit 1
  fi

  download "$iso_url"
  check
  clean
}

if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    main "$@"
fi
```
