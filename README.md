[rh pypi]: https://pypi.python.org/pypi/redis-helper
[rh github]: https://github.com/kenjyco/redis-helper
[settings]: https://github.com/kenjyco/redis-helper/blob/master/settings.ini
[dev-setup]: https://github.com/kenjyco/redis-helper/blob/master/dev-setup.bash

On install, the sample [settings.ini][settings] file will be copied to the
`~/.config/redis-helper` directory.

## Install latest tag of [redis-helper from pypi][rh pypi]

```
% pip install redis-helper
```

## Install latest commit on master of [redis-helper from github][rh github]

```
% pip install git+git://github.com/kenjyco/redis-helper
```

## Local development setup

```
% git clone https://github.com/kenjyco/redis-helper
% cd redis-helper
% python3 setup.py test     # optional, requires 'setuptools'
% ./dev-setup.bash
```

The [dev-setup.bash][dev-setup] script will create a virtual environment in the
`./venv` directory with extra dependencies (ipython, pdbpp, pytest), then copy
`settings.ini` to the `~/.config/redis-helper` directory.

## Running tests in development setup

```
% venv/bin/py.test tests
```

or

```
% venv/bin/py.test -vsx -rs --pdb tests
```

The `py.test` options will run tests in a verbose manner and output the reason
why tests were skipped (if any were skipped). If there are any failing tests,
`py.test` will stop on the first failure and drop you into the debugger.

## Usage

```python
>>> import redis_helper as rh
>>> collection = rh.Collection(..., index_fields='field1,field3')
>>> hash_id = collection.add(field1='', field2='', field3='', ...)
>>> collection.add(...)
>>> collection.add(...)
>>> collection.update(hash_id, field1='', field4='', ...)
>>> change_history = collection.old_data_for_hash_id(hash_id)
>>> data = collection.get(hash_id)
>>> some_data = collection.get(hash_id, 'field1,field3')
>>> results = collection.find(...)
>>> results2 = collection.find('field1:val,field3:val', ...)
>>> results3 = collection.find(..., get_fields='field2,field4')
>>> counts = collection.find(count=True, ...)
>>> top_indexed = collection.index_field_info()
>>> collection.delete(hash_id, ...)
```

## Example

> TODO

## Settings, environments, testing, and debugging

[pdb++]: https://pypi.python.org/pypi/pdbpp/

When using `venv/bin/py.test -vsx -rs --pdb tests`, tests will stop running on
the first failure and drop you into a [pdb++][] debugger session.

To trigger a debugger session at a specific place in the code, insert the
following, one line above where you want to inspect

```
import pdb; pdb.set_trace()
```

To start the debugger inside test code, use

```
pytest.set_trace()
```

- use `(l)ist` to list context lines
- use `(n)ext` to move on to the next statement
- use `(s)tep` to step into a function
- use `(c)ontinue` to continue to next break point (i.e. `set_trace()` lines in
  your code)
- use `sticky` to toggle sticky mode (to constantly show the currently executing
  code as you move through with the debugger)
- use `pp` to pretty print a variable or statement

If the redis server at `redis_url` (in the **test section** of
`~/.config/redis-server/settings.ini`) is not running or is not empty, redis
server tests will be skipped.

Use the `APP_ENV` environment variable to specify which section of the
`settings.ini` file your settings will be loaded from. Any settings in the
`default` section can be overwritten if explicity set in another section.

- if no `APP_ENV` is explicitly set, `dev` is assumed
- the `APP_ENV` setting is overwritten to be `test` no matter what was set when
  calling `py.test` tests

## Background

[dict]: https://docs.python.org/3/tutorial/datastructures.html#dictionaries
[hash]: http://redis.io/commands#hash
[Redis]: http://redis.io/topics/data-types-intro
[redis-py]: https://github.com/andymccurdy/redis-py
[StrictRedis]: https://redis-py.readthedocs.org/en/latest/#redis.StrictRedis

A [Python dictionary][dict] is a very useful **container** for grouping facts
about some particular entity. Dictionaries have **keys** that map to
**values** (so if we want to retrieve a particular value stored in a dictionary,
we can access it through its key). The dictionary itself is accessed by its
**variable name**.

[Redis][] is a **data structure server** (among other things). It is great for
storing various types of objects that can be accessed between different programs
and processes. When your program stops running, objects that you have stored in
Redis will remain. To retreive an object from Redis, you must access it through
its **key name** (kind of like a Python variable name). The
[redis Python package][redis-py] provides the [StrictRedis][] class, which
contains methods that correspond to all of the Redis server commands.

A [Redis hash][hash] is most similar to a Python dictionary. A "key" in a Python
dictionary is analogous to a "field" in a Redis hash (since "key" means
something different in Redis).
