Fasten PyPI Resolver
===============

This tool resolves Python dependencies by using `pip`.
It can be executed as a CLI tool, or it can be deployed as a Flask API.
It accepts as input any string that can be resolved by pip.
It also supports dependency resolution of local python projects, by parsing the requirements.txt file.

Command Line Arguments
----------------------
__You should always use at least one of -f, -i or -r__

```
usage: Resolve dependencies of PyPI packages [-h] [-i INPUT_PACKAGE] [-r REQUIREMENTS_FILE] [-o OUTPUT_FILE] [-f]

optional arguments:
  -h, --help            show this help message and exit
  -i INPUT_PACKAGE, --input-package INPUT_PACKAGE
                        Input package be a string of a package name or the names of multiple
                        packages separated by spaces. Examples: 'django' or
                        'django=3.1.3' or 'django wagtail'
  -r REQUIREMENTS_FILE, --requirements-file REQUIREMENTS_FILE
                        The path of the requirements.txt file.
                        When specified, the dependencies of a local project are
                        resolved
  -o OUTPUT_FILE, --output-file OUTPUT_FILE
                        File to save the output
  -f, --flask           Deploy flask api
```

Output Format
-------------

The tool returns a JSON object.
If it managed to resolve the package successfully,
then it returns a JSON in the following format.
Note that in `packages` key, there is also the package that was given as input.

```json
{
  "input": "wagtail",
  "status": true,
  "packages": {
    "django-taggit": {
      "package": "django-taggit",
      "version": "1.3.0"
    },
    "jdcal": {
      "package": "jdcal",
      "version": "1.4.1"
    },
    "Pillow": {
      "package": "Pillow",
      "version": "8.0.1"
    },
    "pytz": {
      "package": "pytz",
      "version": "2020.4"
    },
    ...
}
```

Otherwise, it produces a JSON with an error message.

```json
{
    "input": "bbq",
    "status": false,
    "error": "ERROR: Could not find a version that satisfies the requirement
    bbq (from versions: none)"
}
```


## Micro-service

Deploy a micro-service that exposes a REST API for resolving Python dependencies.

```bash
docker build -f Dockerfile -t pypi-resolver-api .
docker run -p 5001:5000 pypi-resolver-api
```

### Dependency Resolution Endpoint for PyPI Packages

* Request format

```
url: http://localhost:5001/dependencies/{packageName}/{version}
```
<b>Note:</b> The {version} path parameter is optional

* Example request using curl:

```bash
curl "http://localhost:5001/dependencies/django/3.1.3"
```

* Output format:
 
 ```json
[
  {
    "product": "Django",
    "version": "3.1.3"
  },
  {
    "product": "sqlparse",
    "version": "0.4.2"
  },
  {
    "product": "asgiref",
    "version": "3.4.1"
  },
  {
    "product": "pytz",
    "version": "2021.1"
  }
]
```
### Dependency Resolution Endpoint for multiple dependencies.


* Request format

```
url: http://localhost:5001/resolve_dependencies
```

* Usage

  Should recieve through a POST Request a list of dependencies as defined on the requirements.txt file, separated by commas 

* Example request using curl:

```bash
 curl -X POST -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '[flask, pip-tools]' "http://localhost:5001/resolve_dependencies"
```

* Output format:
 
 ```json
[
  {
    "product": "tomli", 
    "version": "2.0.1"
  }, 
  {
    "product": "pip", 
    "version": "22.0.4"
  }, 
  {
    "product": "zipp", 
    "version": "3.7.0"
  }, 
  {
    "product": "Jinja2", 
    "version": "3.1.1"
  }, 
  {
    "product": "setuptools", 
    "version": "61.2.0"
  }, 
  {
    "product": "Werkzeug", 
    "version": "2.1.0"
  }, 
  {
    "product": "Flask", 
    "version": "2.1.0"
  }, 
  {
    "product": "importlib-metadata", 
    "version": "4.11.3"
  }, 
  {
    "product": "pep517", 
    "version": "0.12.0"
  }, 
  {
    "product": "itsdangerous", 
    "version": "2.1.2"
  }, 
  {
    "product": "click", 
    "version": "8.1.0"
  }, 
  {
    "product": "MarkupSafe", 
    "version": "2.1.1"
  }, 
  {
    "product": "pip-tools", 
    "version": "6.5.1"
  }, 
  {
    "product": "wheel", 
    "version": "0.37.1"
  }
]
```
