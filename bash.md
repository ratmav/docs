bash
====

[reference](https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html)

# etc

## run a command as a user w/o a login shell

```
[root@hostname ~]# su -s /bin/bash -c "whoami" systemuser
```
