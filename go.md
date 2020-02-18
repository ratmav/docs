go
==

* [documentation](https://golang.org/doc/)

# version management

## macos

1. install [gvm](https://github.com/moovweb/gvm):
    ```bash
    $ bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
    ```
2. confirm the following is in your shell configuration:
    ```bash
    [[ -s "/Users/youruser/.gvm/scripts/gvm" ]] && source "/Users/youruser/.gvm/scripts/gvm"
    ```

# installation

```bash
gvm install go1.11.6 --binary
gvm use go1.11.6 --default
```

# tools

* debugger: [delve](https://github.com/go-delve/delve)
* repl: [gore](https://github.com/motemen/gore)
