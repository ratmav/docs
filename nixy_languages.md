*nixy languages
===============

## version management

1. install [asdf](https://github.com/asdf-vm/asdf)
    * macos:
        1. install [homebrew](https://brew.sh/): `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"`
        2. install [asdf](https://asdf-vm.com/#/): `git clone https://github.com/asdf-vm/asdf.git $HOME/.asdf`
2. install language-specific plugins (see below)
3. configure your shell:
    ```shell
    $ echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.bashrc # or .bash_profile, etc.
    $ echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bashrc # or .bash_profile, etc.
    ```

## bash

### redirect stdout and stderr to file

```bash
command1 >> log_file 2>&1 # same file
command1 >> log_file 2>> err_file # different files
```

## python

[asdf python plugin](https://github.com/danhper/asdf-python): `asdf plugin-add python`

### reload classes in the interpreter

**note**: tested on v2.7

```bash
$ python
>>> import module.submodule
>>> foo = module.submodule.yourclass()
>>> foo.version()
v1
>>> reload(module.submodule)
>>> foo = module.submodule.yourclass()
>>> foo.version()
v2
```

### autoload virtualenv from script

**note**: works on 2.7 and 3.6

```python
#!/usr/bin/env python


# force virtualenv.
import os
directory = os.path.dirname(os.path.abspath(__file__))
activate = os.path.join(directory, '../.your-project_env/bin/activate_this.py')
execfile(activate, dict(__file__=activate))

# your code here...
```

### pretty print json

**note**: should word on v2.6+; tested on v2.7

```shell
echo '{"foo": "lorem", "bar": "ipsum"}' | python -m json.tool # directly on cli
python -m json.tool my_json.json # json in file.
```

## go

[asdf go plugin](https://github.com/kennyp/asdf-golang): `asdf plugin-add golang https://github.com/kennyp/asdf-golang.git`

### tools

some packages you may find useful; don't add these to `go.mod`:

* [delve](https://github.com/go-delve/delve)
    * `go get github.com/go-delve/delve/cmd/dlv`
* [golint](https://github.com/golang/lint)
    * `go get -u golang.org/x/lint/golint`
* [gore](https://github.com/motemen/gore)
    * `go get -u github.com/motemen/gore/cmd/gore`

### module management

run the following when imports are modified.

```shell
$ go mod init example.com/hello # run once when creating a new project/module. you may need to use the full path to the repo, for example "example.com/foo/bar/hello.git"
$ go mod vendor # creates the vendor folder for dependencies.
$ go mod download # downloads module dependencies, including indirects, to the vendor folder.
$ go mod tidy # cleans up modules based on current imports. run after importing new external packages.
$ go mod verify # confirms all modules are downloaded, checksums are correct, no missing packages, etc.
$ go clean --modcache # run when you want to wipe out all local vendored modules. probably a good idea to manually remove the vendor folder first.
```

#### updating vendored modules

typically, you just `go get -u the.domain/foo/bar/baz/package_name` to install or update a vendored package. by default, `go mod` will attempt to pull **all** modules down, which you might not want or need (like in a ci/cd container). in that case, when you _just_ want to pull down a single specific package, use the `-f` flag: `go get -f -u the.domain/foo/bar/baz/package_name`.

### pre-push checks

```shell
$ go fmt
$ go test
$ go vet
$ golint # or whatever linter the project is using.
```

### pretty print json from struct

```go
import "fmt"

type Foo struct {
	ID  string    `json:"id"`
	Woo string    `json:"woo"`
	Lad string    `json:"lad"`
}

newFoo := Foo{}
newFoo.ID = "D34DB33F"
newFoo.Woo = "goodTimes.jpg"
newFoo.Lad = "Ric Flair"

foo, _ := json.MarshalIndent(newFoo, "", "  ")
fmt.Printf("%s\n", foo)
```

## ruby

[asdf ruby plugin](https://github.com/asdf-vm/asdf-ruby): `asdf plugin-add ruby https://github.com/asdf-vm/asdf-ruby`

## hcl (terraform)

[asdf terraform plugin](https://github.com/Banno/asdf-hashicorp)

## node.js

[asdf node.js plugin](https://github.com/asdf-vm/asdf-nodejs):
    ```shell
    $ asdf plugin-add nodejs https://github.com/asdf-vm/asdf-nodejs.git
    $ bash ~/.asdf/plugins/nodejs/bin/import-release-team-keyring
    ```
