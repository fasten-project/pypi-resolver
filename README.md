pypi-resolver
===============

This tool resolves Python dependencies by using `pip`.
It can be executed as a CLI tool, or it can be deployed as a Flask API.
It accepts as input any string that can be resolved by pip.

Command Line Arguments
----------------------
__You should always use at least one of -f or -i__

```
usage: Resolve dependencies of PyPI packages [-h] [-i INPUT] [-o OUTPUT_FILE] [-f]

optional arguments:
  -h, --help            show this help message and exit
  -i INPUT, --input INPUT
                        Input should be a string of a package name or the names of multiple
                        packages separated by spaces. Examples: 'django' or
                        'django=3.1.3' or 'django wagtail'
  -o OUTPUT_FILE, --output-file OUTPUT_FILE
                        File to save the output
  -f, --flask           Deploy flask api
```

Flask API
---------
* **Endpoint**: `/api/v1/packages`
* **Supported query parameters**: input (required)

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
