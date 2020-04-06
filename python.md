python
======

## reload classes in the interpreter

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

## autoload virtualenv from script

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
