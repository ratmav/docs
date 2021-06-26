ssh
===

## general information on pki and mfa

  * [public key infrastructure](http://en.wikipedia.org/wiki/public-key_infrastructure)
  * [multi-factor authentication](http://en.wikipedia.org/wiki/multi-factor_authentication)

### pki do's and don'ts

  * **do not** write your passphrase in a publicly-readable place.
  * **do not** share your passphrase with anyone.
  * **do not** use a blank passphrase.
  * **do not** write your private key in a publicly-readable place.
  * **do not** share your private key with anyone.
  * **do** share your _public_ key.

## local keys

## permissions

* `~/.ssh` (directory): `700 (drwx------)`
* `~/.ssh/key_name.pub` (public keys): `644 (-rw-r--r--)`
* `~/.ssh/key_name` (private keys): `600 (-rw-------)`
* `~/.ssh/authorized_keys` (public keys allowed by server): `644 (-rw-r--r--)`
 
### generate a keypair

```bash
 $ cd ~/.ssh
 $ ssh-keygen -b 4096 -f ./username_domain
```

### use private keys with non-default names

```bash
 $ echo "identityfile ~/.ssh/firstname-lastnme" >> ~/.ssh/config
```

### generate public key from private key

```bash
$ ssh-keygen -l -f ~/.ssh/id_rsa  # confirm public key not already present in cwd.
$ ssh-keygen -y -f ~/.ssh/id_rsa > ~/.ssh/id_rsa.pub # generate public key.
$ ssh-keygen -l -f ~/.ssh/id_rsa # confirm new public key matches existing private key.
```

### configure client for multiple keys

#### `~/.ssh/config`

```
host foo.companyname.com
  addkeystoagent yes
  usekeychain yes
  identityfile ~/.ssh/firstname-lastname_companyname

host *
  addkeystoagent yes
  usekeychain yes
  identityfile ~/.ssh/default-keyname
  identitiesonly yes
```

### configure local ssh agent

**note**: be sure that the key is added/configured in the `~/.ssh/config` file!

#### start the ssh agent

* bash: `$ eval "$(ssh-agent -s)"`
* powershell (needs admin rights):
   ```powershell
   > Set-Service -Name ssh-agent -StartupType Automatic
   > Start-Service ssh-agent
   ```

#### add key to ssh agent

```
$ ssh-add -k ~/.ssh/whatever-key-you-want
```

## remote keys

### create list of authorized keys

```bash
 $ touch ~/.ssh/authorized_keys
```

### append new key to list of authorized keys

```bash
 # after scp'ing key and changing key file permissions.
 $ cat ~/key.pub >> ~/.ssh/authorized_keys
 $ rm -f ~/key.pub
```
