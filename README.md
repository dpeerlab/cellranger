# Building Cell Ranger 3.0.2
## Build dependencies
- Python 2.7.13
- rust 1.28.0
- clang 6.0
- go 1.11
- node v8.11.4

## Building on AWS Ubuntu 16.04

This was tested on AWS Ubuntu 16.04 (`ami-0f9cf087c1f27d9b1`).

The `golang` package is not shipped out of the box. Add this repository (https://github.com/golang/go/wiki/Ubuntu) before installing `golang`:

```bash
$ sudo add-apt-repository -y ppa:gophers/archive
```

Make the software package information up-to-date:

```bash
$ sudo apt-get update -y
```

Install the build dependencies such as `make`, `golang`, `clang`, and etc.:

```bash
$ sudo apt-get install -y make clang-6.0 golang-1.11-go libz-dev libbz2-dev liblzma-dev
```

Add `golang` to path:

```bash
$ export PATH=/usr/lib/go-1.11/bin:$PATH
```

Install `Rust`:

```bash
$ curl https://sh.rustup.rs -sSf | sh -s -- -y
$ source $HOME/.cargo/env
```

Download Cell Ranger v3.0.2 and decompress it:

```
$ wget https://github.com/10XGenomics/cellranger/archive/3.0.2.tar.gz
$ tar xvzf 3.0.2.tar.gz
```

```
$ cd cellranger-3.0.2/
```

Before executing `make` to build Cell Ranger, install `Cython` via `pip`, otherwise you will get this error:

```
mkdir -p /home/ubuntu/cellranger/lib/cython/lib/python/cellranger && cython --line-directives  -w /home/ubuntu/cellranger/lib/python/cellranger -o /home/ubuntu/cellranger/lib/cython/lib/python/cellranger/report.c report.pyx
/bin/bash: cython: command not found
Makefile:50: recipe for target '/home/ubuntu/cellranger/lib/cython/lib/python/cellranger/report.c' failed
make: *** [/home/ubuntu/cellranger/lib/cython/lib/python/cellranger/report.c] Error 127
```

Install `pip` and `Cython`:

```bash
$ sudo apt-get install -y python-pip
$ pip install cython
```

Install `numpy` using Ubuntu's `apt-get`, not Python's `pip`, otherwise you will get this error:

```
    -I/numpy/core/include \
    -o /home/ubuntu/cellranger/lib/cython/lib/python/cellranger/bisect.o \
    /home/ubuntu/cellranger/lib/cython/lib/python/cellranger/bisect.c
/home/ubuntu/cellranger/lib/cython/lib/python/cellranger/bisect.c:593:31: fatal error: numpy/arrayobject.h: No such file or directory
compilation terminated.
```

Install `numpy` using Ubuntu's `apt-get`:

```bash
$ sudo apt-get install -y python-numpy
```

Finally, run `make` to build Cell Ranger:

```bash
$ make
```

Make sure you can run the executables:

```bash
$ cd bin
$ $ ./cellranger --help
/home/ubuntu/cellranger-3.0.2/bin
cellranger --help ()
Copyright (c) 2018 10x Genomics, Inc.  All rights reserved.
-------------------------------------------------------------------------------

Usage:
    cellranger mkfastq

    cellranger count
    cellranger aggr
    cellranger reanalyze
    cellranger mat2csv

    cellranger mkgtf
    cellranger mkref

    cellranger vdj

    cellranger mkvdjref

    cellranger testrun
    cellranger upload
    cellranger sitecheck
```

# Running Cell Ranger
## Runtime dependencies
- Binary dependencies can be found in the Ranger 3.0.2 package (https://support.10xgenomics.com/developers/software/downloads/latest)
  - The Ranger package includes a build of the Martian platform (v3.2.0), which is open source. For more information, go to http://martian-lang.org/ .

## Setting up the environment
```
# Setup Martian and binary dependencies
source /path/to/ranger/sourceme.bash

# Setup Cell Ranger
source /path/to/cellranger/sourceme.bash
```

## Note about Loupe
The binaries required to generate Loupe Cell Browser (.cloupe) and Loupe V(D)J Browser files (.vloupe) are not included in this repository or in the binary dependencies package Ranger. The necessary binaries can be obtained from an existing binary version of Cell Ranger by running:
`cp /path/to/cellranger-3.0.2/cellranger-cs/*/lib/bin/{crconverter,vlconverter} /path/to/open-source-cellranger/lib/bin/`

# Support
We do not provide support for building and running this code.

The officially supported release binaries are available at: (https://support.10xgenomics.com/single-cell-gene-expression/software/downloads/latest)
