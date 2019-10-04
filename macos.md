macos
=====

# keep network interfaces up when locked

of course, taken from [stack overflow](https://apple.stackexchange.com/questions/71884/wi-fi-disconnects-when-i-lock-the-mac#97047)....

```bash
# after getting the mac of the wifi interface, get the name of the interface.
$ ifconfig

# set "disconnectonlogout" to "no".
sudo ./airport $interface_name_probably_en0 prefs disconnectonlogout=no
```
