bash
====

## redirect stdout and stderr to file

```bash
command1 >> log_file 2>&1 # same file
command1 >> log_file 2>> err_file # different files
```
## tools

* linting: [shellcheck](https://github.com/koalaman/shellcheck)
* testing: [bats](https://github.com/bats-core/bats-core)
