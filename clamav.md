clamav
======

[documentation](https://www.clamav.net/documents/clam-antivirus-user-manual)

## install

```bash
$ sudo dnf install -y clamav-server clamav-data clamav-update clamav-filesystem clamav clamav-scanner-systemd clamav-devel clamav-lib clamav-server-systemd
```

## configure selinux

```bash
$ sudo setsebool -p antivirus_can_scan_system 1
```

## configure `clamd*`

### config file

#### copy config file

```bash
$ sudo cp /usr/share/clamav/template/clamd.conf /etc/clamd.d/clamd.conf
$ sudo sed -i '/^example/d' /etc/clamd.d/clamd.conf
```

#### modify config file

##### `user`

###### before

```bash
user <user>
```

###### after

```bash
user clamscan
```

##### `localsocket`

###### before

```bash
#localsocket /var/run/clamd.<service>/clamd.sock
```

###### after

```bash
localsocket /var/run/clamd.scan/clamd.sock
```

### rename `clamd*` services

```bash
$ cd /usr/lib/systemd/system
$ sudo mv clamd@.service clamd.service
$ sudo mv clamd@scan.service clamdscan.service
```

### update `clamdscan` service

**note**: the only change is removing the `@` from the top level include statement.

#### before

```bash
.include /lib/systemd/system/clamd@.service
```

#### after

```bash
.include /lib/systemd/system/clamd.service
```

### update `clamd` service

replace the contents of `/usr/lib/systemd/system/clamd.service` with:

```bash
[unit]
description = clamd scanner daemon
after = syslog.target nss-lookup.target network.target
[service]
type = simple
execstart = /usr/sbin/clamd -c /etc/clamd.d/clamd.conf --foreground=yes
restart = on-failure
privatetmp = true

[install]
wantedby=multi-user.target
```

### enable and start `clamd*` services

```bash
$ sudo systemctl enable clamd.service
$ sudo systemctl start clamd.service
$ sudo systemctl enable clamdscan.service
$ sudo systemctl start clamdscan.service

```

**note**: this is _performance intensive_. it's going to constantly scan the whole system. budgeting hardware resources for this is probably worth it on an exposed production system, but internal systems may be better off with these services installed for convenience, but stopped and disabled most of the time. ymmv.


## configure `freshclam`

```bash
$ sudo sed -i '/^example/d' /etc/freshclam.conf
```

### create `freshclam` service

write the following content to `/usr/lib/systemd/system/freshclam.service`:

```bash
# run the freshclam as daemon
[unit]
description = freshclam scanner
after = network.target
[service]
type = forking
execstart = /usr/bin/freshclam -d -c 4
restart = on-failure
privatetmp = true
[install]
wantedby=multi-user.target
```

### enable and start `freshclam` service

```bash
$ sudo systemctl enable freshclam.service
$ sudo systemctl start freshclam.service
```
