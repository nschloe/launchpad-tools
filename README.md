Launchpad tools
===============

[![Build Status](https://travis-ci.org/nschloe/launchpadtools.svg?branch=master)](https://travis-ci.org/nschloe/launchpadtools)
[![Code Health](https://landscape.io/github/nschloe/launchpadtools/master/landscape.svg?style=flat)](https://landscape.io/github/nschloe/launchpadtools/master)
[![PyPi Version](https://img.shields.io/pypi/v/launchpadtools.svg)](https://pypi.python.org/pypi/launchpadtools)


Some tools for easy submission to launchpad.


### Usage

All options are documented under `launchpad-submit -h`.

Sometimes, you may want to submit a source package with a Debian configuration
that is available somewhere else. This may help setting up a nightly submission
process. As an example, take the nightly submission script for a
[Mixxx PPA](https://launchpad.net/~nschloe/+archive/ubuntu/mixxx-nightly).

```
#!/bin/sh -ue

TMP_DIR=$(mktemp -d)
cleanup() { rm -rf "$TMP_DIR"; }
trap cleanup EXIT

ORIG_DIR="$TMP_DIR/orig"
clone --ignore-hidden \
  "https://github.com/mixxxdj/mixxx.git" \
  "$ORIG_DIR"

VERSION=$(grep "define MIXXX_VERSION" "$ORIG_DIR/src/defs_version.h" | sed "s/[^0-9]*\([0-9][\.0-9]*\).*/\1/")
FULL_VERSION="$VERSION~$(date +"%Y%m%d%H%M%S")"

DEBIAN_DIR="$TMP_DIR/orig/debian"
clone \
  --subdirectory=debian/ \
  "git://anonscm.debian.org/git/pkg-multimedia/mixxx.git" \
  "$DEBIAN_DIR"

launchpad-submit \
  --work-dir "$TMP_DIR" \
  --ubuntu-releases trusty xenial yakkety zesty \
  --ppa nschloe/mixxx-nightly \
  --version-override "$FULL_VERSION" \
  --version-append-hash \
  --update-patches
```

### Installation

#### Python Package Index

The launchpad tools are [available from the Python Package
Index](https://pypi.python.org/pypi/launchpadtools/), so for
installation/upgrading simply do
```
pip install -U launchpadtools
```

#### Manual installation

Download the launchpad tools from
[the Python Package Index](https://pypi.python.org/pypi/launchpadtools/).
Place the launchpad tools in a directory where Python can find it (e.g.,
`$PYTHONPATH`).  You can install it system-wide with
```
python setup.py install
```

### Distribution
To create a new release

1. bump the `__version__` number and

2. tag and upload to PyPi:
    ```
    $ make publish
    ```

### License

The launchpadtools are published under the [MIT license](https://en.wikipedia.org/wiki/MIT_License).
