# pgp

* [gnupg manual](https://www.gnupg.org/documentation/manuals/gnupg/)

## create

```shell
$ gpg --full-generate-key
```
## read/export

```shell
$ gpg --list-secret-keys --keyid-format=long
$ gpg --armor --export <GPG KEY ID>
```

## backup

```shell
# 1. export pubkey
gpg --armor --export <EMAIL> > public.asc

# 2. export private key
# you will be prompted for your passphrase
gpg --armor --export-secret-keys <EMAIL> > secret.asc

# 3. export owner trust db
gpg --export-ownertrust > ownertrust.txt

# 4. generate revocation certificate
# store this separately; it is vital if you lose your key
gpg --armor --gen-revoke <EMAIL> > revocation.asc
```

## restore

```shell
# 1. import keys
# secret.asc already contains the public key; you will be prompted for your passphrase
gpg --import secret.asc

# 2. import owner trust db
# without this the restored key shows [unknown] instead of [ultimate]
gpg --import-ownertrust ownertrust.txt

# 3. verify
gpg --list-secret-keys --keyid-format=long

# 4. (only to retire a lost/compromised key) apply the revocation certificate
# this revokes the key; do not run this during a normal restore
gpg --import revocation.asc
```

## use with git

```shell
$ git config --global --unset gpg.format
$ gpg --list-secret-keys --keyid-format=long
$ git config --global user.signingkey <GPG KEY ID>
$ git config --global commit.gpgsign true
$ git config --global tag.gpgSign true
```

