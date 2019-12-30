python
======

* [documentation](https://www.python.org/doc/)
* [the hitchhiker's guide to python](http://docs.python-guide.org/en/latest/)
    * **NOTE**: this suggests using [pipenv](https://pipenv.readthedocs.io/en/latest/), which frankly doesn't integrate well with many projects due to community disagreements. use `pyenv` and `virtualenv` instead.

# version management

## macos

1. install [homebrew](https://brew.sh/): `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
2. install [pyenv](https://github.com/pyenv/pyenv): `brew install pyenv`
3. install [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv): `brew install pyenv-virtualenv`
4. add the following to your shell configuration:
    ```bash
    eval "$(pyenv init -)"
    eval "$(pyenv virtualenv-init -)"
    ```

# reload classes in the interpreter

## 2.7

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

## autoload virtualenv from script

*note*: works on 2.7 and 3.6

```python
#!/usr/bin/env python


# force virtualenv.
import os
directory = os.path.dirname(os.path.abspath(__file__))
activate = os.path.join(directory, '../.your-project_env/bin/activate_this.py')
execfile(activate, dict(__file__=activate))


# your code here...
```
