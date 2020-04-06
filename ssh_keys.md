ssh keys
========

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

### generate a keypair

```bash
      $ cd ~/.ssh
      $ ssh-keygen -b 4096 -c "your.email.address@domain.tld" -f ./firstname-lastname
```

### use private keys with non-default names

```bash
      $ echo "identityfile ~/.ssh/firstname-lastnme" >> ~/.ssh/config
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

```
$ eval "$(ssh-agent -s)"
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
