# url2vapi (url to versioned api)

It's is tiny package that provides one function `split`. Function takes two
arguments: url and pattern, then attempts to parse the url according to the
pattern.  Returning result is new object that contains all matched variables +
reminding part of the url. Package for most part uses standard urllib.parse
with a bits added on top of it.

## Example usage:

```python

import url2vapi

print(url2vapi.split('http://www.example.com/').as_dict())
{'domain': 'www.example.com', 'port': None, 'protocol': 'http', 'remainder': ''}

print(url2vapi.split('http://www.example.com/dd/ff/').as_dict())
{'domain': 'www.example.com', 'port': None, 'protocol': 'http', 'remainder': 'dd/ff/'}

print(url2vapi.split('http://www.example.com/3.0/dd/ff/').as_dict())
{'domain': 'www.example.com', 'port': None, 'protocol': 'http', 'remainder': '3.0/dd/ff/'}

print(url2vapi.split('http://www.example.com/3.0/dd/ff/', pattern='<version:number>').as_dict())
{'domain': 'www.example.com', 'port': None, 'protocol': 'http', 'remainder': 'dd/ff/', 'version': {'prefix': '', 'suffix': '', 'value': 3}}

print(url2vapi.split('http://www.example.com/3.0/dd/ff/', pattern='<version:double>').as_dict())
{'domain': 'www.example.com', 'port': None, 'protocol': 'http', 'remainder': 'dd/ff/', 'version': {'prefix': '', 'suffix': '', 'value': 3.0}}

print(url2vapi.split('http://www.example.com/3.0/letters/c/', pattern='<version:double>/<namespace>').as_dict())
{'port': None, 'namespace': {'prefix': '', 'suffix': '', 'value': 'letters'}, 'version': {'prefix': '', 'suffix': '', 'value': 3.0}, 'domain': 'www.example.com', 'protocol': 'http', 'remainder': 'c/'}

# or something more exotic:

print(url2vapi.split(
    'http://www.example.com/v3_1.1_0.1-b1/3/a/',
    pattern='v<version:tuple[double]:_>-b1>/<namespace_number:number>').as_dict())
{'port': None, 'version': {'prefix': 'v', 'suffix': '-b1', 'value': (3.0, 1.1, 0.1)}, 'domain': 'www.example.com', 'protocol': 'http', 'remainder': 'a/', 'namespace_number': {'prefix': '', 'suffix': '', 'value': 3}}
```

## Available patterns

Pattern is a string that should contain groups, each group is defined as follows:

```
    (optional_prefix)<name_of_the_group:optional_type:optional_delimiter>(optional_suffix)
```

Where:

    - optional_prefix - is any alphanumeric range of characters, defaults to '' (with addition of some common characters like `_-\.,`)
    - name_of_the_group - is mandatory alphanumeric range of one or more characters (underscores are available) 
    - optional_type can be one of the following: `number`, `double`, `string`, `bool` or `tuple`, defaults to `string`
    - if type is set to `tuple`, we can specify delimiter (ie. characters `.,|_-`)
    - tuple can specify subtype in a form `tuple[double]` or `tuple[bool]` but not `tuple[tuple]`
    - optional_suffix - is any alphanumeric range of characters, defaults to '' (with addition of some common characters like `_-\.,`)

## Testing

Git clone repository, pip install dependency and then run nosetests by simply `./bin/run_tests.sh` (this will open browser with coverage result)

Or

`python setup.py test`
