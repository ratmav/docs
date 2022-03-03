python
======

## version management

* *nix: install [asdf](https://asdf-vm.com/)
    * [asdf python plugin](https://github.com/danhper/asdf-python): `asdf plugin-add python`
* windows: install [pyenv-win](https://pyenv-win.github.io/pyenv-win/)

## snippets

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

**note**: works on 3.8

```python
#!/usr/bin/env python

# autoload virtualenv.
import os

directory = os.path.dirname(os.path.abspath(__file__))
if os.name == 'nt': # windows
  activate = os.path.join(directory, '../your-virtual-env-directory/Scripts/activate_this.py')
else:
  activate = os.path.join(directory, '../your-virtual-env-directory/bin/activate_this.py')
execfile(open(activate).read())

# your code here...
```

### pretty print json

**note**: should word on v2.6+; tested on v2.7

```shell
echo '{"foo": "lorem", "bar": "ipsum"}' | python -m json.tool # directly on cli
python -m json.tool my_json.json # json in file.
```

