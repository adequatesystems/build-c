<h1 align="center">
   <a href="http://adequate.biz">
      <img alt="Adequate Systems" src="https://raw.githubusercontent.com/adequatesystems/.github/main/media/adqlogo_banner.svg" /></a>
   <br/>C/C++ Build and Workflow Support<br/>
   <a href="https://github.com/adequatesystems/build-c/tags">
      <img src="https://img.shields.io/github/v/tag/adequatesystems/build-c?color=0059ff&label=Latest&labelColor=2d3339&logo=github&logoColor=lightgray" alt="GitHub tag (latest by date)"></a>
   <a href="LICENSE.md">
      <img src="https://img.shields.io/badge/_License-MPL_2.0_Derivative-%23.svg?logoColor=lightgreen&logo=open%20source%20initiative&labelColor=2d3339&color=0059ff" alt="MPL 2.0 Derivative" /></a>
</h1>

This repository is designed for inclusion in C/C++ projects for easy build and workflow integration, intended for use in ongoing projects at Adequate Systems, LLC.

## Instructions
**NOTE: This repository contains it's own LICENSE.md and README.md files that may introduce artifacts in existing files of the target repostiory. Please ensure the LICENSE.md and README.md files are appropriate and/or correct for your target repostiory pulling this repository.**

To include or update, it makes no difference; add this repository as a remote and pull the latest (or desired) revision, specifying to "allow unrelated histories"...<br/>
<sup><i>Adding the repository as a remote may be omitted if previously performed</i></sup>
```sh
git remote add build-c https://github.com/adequatesystems/build-c.git
git pull build-c main --allow-unrelated-histories
```
Fix merge conflicts and commit with...<br/>
<sup><i>Commit may also be performed selectively</i></sup>
```sh
git add <any files with merge conflicts>
git commit -m "merge latest build-c repository files"
```
To upgrade/downgrade at a later date...<br/>
<sup><i>Recommend using version tags like "build-c-<version>"</i></sup>
```sh
git pull build-c build-c-1.1.2
git add <any files with merge conflicts>
git commit -m "merge build-c v1.1.2 repository files"
```

## Typical active project directory structure
```diff
# Configuration / Static
+ Manually managed
- Automatically managed
! Automatically generated only

# base-c
# ├── .github
# │   ├── docs
# │   │   ├── .nojekyll
# │   │   ├── config
# │   │   ├── layout.xml
# │   │   ├── logo.svg
# │   │   └── style.css
# │   └── workflows
# │       ├── codeql.yaml
# │       └── tests.yaml
- ├── build
- │   ├── bin
- │   │   ├── alternate-binary
- │   │   └── main-binary
- │   ├── test
- │   │   ├── sourcetest-cu.d
- │   │   ├── sourcetest-cu.o
- │   │   ├── sourcetest-cu
- │   │   ├── sourcetest.d
- │   │   ├── sourcetest.o
- │   │   └── sourcetest
- │   ├── source
- │   └── source.cu.o
- │   ├── source.d
- │   └── source.o
! ├── docs
! │   └── htmlfiles...
! ├── include
! │   └── submoduledirs...
+ ├── src
+ │   ├── bin
+ │   │   ├── alternate-binary.c
+ │   │   └── main-binary.c
+ │   ├── test
# ├── │   ├── _assert.h
+ │   │   ├── sourcetest-cu.c
+ │   │   └── sourcetest.c
+ │   ├── source.c
+ │   ├── source.cu
+ │   ├── source.cuh
+ │   └── source.h
# ├── .gitignore
# ├── CHANGELOG.md
# ├── GNUmakefile
# ├── LICENSE.md
# └── README.md
```

## Automatically configured filename/extension support
By default, the GNUMakefile is automatically configured to operate with the following filenames and extensions:
- `src/*.c:` C source file, compiled with `$(CC)`
- `src/*.h:` C header file, for inclusion by other source files
- `src/*.cu:` CUDA source file, compiled with `$(NVCC)`
- `src/*.cuh:` CUDA header file, for inclusion ONLY by other CUDA source files
- `src/bin/*.c:` C source file, for binary execution
- `src/test/component-*.c:` C source file for testing C functions
- `src/test/component-*-cu.c:` C source file for testing CUDA functions

## Makefile usage
CLI usage information is revealed with `make help`:
```
Usage:  make [options] [targets]
Options:
   DEFINES="<defines>" for additional preprocessor definitions
      e.g. make all DEFINES="_GNU_SOURCE _XOPEN_SOURCE=600"
   NO_CUDA=1 to disable CUDA support
   NO_RECURSIVE=1 to disable recursive submodule actions
   CFLAGS="<flags>" for additional C compiler flags
   NVCFLAGS="<flags>" for additional NVIDIA compiler flags
      e.g. make all CFLAGS="-fsanitize=address"
   ... "make --help" for make specific options
User Targets:
   ... no user targets available ...
Utility Targets:
   make [all]       build all object files into a library
   make clean       remove build files (incl. within submodules)
   make coverage    build test coverage file and generate report
   make docs        build documentation files
   make echo-*      show the value of a variable matching *
   make help        prints this usage information
   make test        build and run (all) tests
   make test-*      build and run tests matching *
```

## Configurable Flags
Most parameters used by the Makefile can be configured, either directly in the Makefile itself or on the command line by appending the flag and its value to the call to `make`. For a complete list of FLAGS it is recommended to peruse the GNUmakefile source.

## Integrated Documentation
*Requires at least `doxygen` v1.9.x (unavailable through `apt` on Ubuntu 20.04)*

C/C++ Documentation is made available with the help of Doxygen, using special comment style blocks before functions and definitions for automatic recognition and compilation into an easy to navigate html documentation front-end.

Use:
* `make docs` after the code is commented appropriately

## Integrated Testing
Test files should be placed in the `src/test/` directory as `*.c` source files. Every test file will be compiled and run as it's own binary. So whether a test is broad or specific, a single test file can only count as a single failure.

Use:
* `make test` to run all tests, OR
* `make test-*` to run all tests matching `*`

## Test coverage (local)
<sup><i>Note: Local test coverage may be incomplete if tests fail</i></sup>

Test coverage can be generated locally and viewed via a HTML report (generated separately). `lcov` is required to generate coverage data.

Use:
* `make coverage` to generate html report from coverage data

## Submodule support
Support for submodules is automatically built into the Makefile, provided that:
* the submodules use the same build and workflow structure
* the submodules are added to the `include/` directory

### Add a submodule
Adding a submodule can be done as part of a larger commit if desired.
```sh
cd project-repo
git submodule add <submodule-repo> include/<submodule-name>
git commit -m "add submodule to repostory"
```

### Update a submodule
**Operating the Makefile between any of the steps for updating a submodule may result in a misconfigured submodule.** It is recommended to complete steps below before operating the makefile. Updating a submodule can be done as part of a larger commit if desired.
```sh
cd project-repo
git -C include/<submodule-name> pull origin <main|tag|commit>
git add include/<submodule-name>
git commit -m "update submodule to <main|tag|commit>"
```

## CUDA support
CUDA compilation of `*.cu` source files is enabled BY DEFAULT for systems with appropriately installed CUDA Toolkit. The Makefile uses the `nvcc` compiler in place of the normal compiler (normally gcc) to compile identical object files. By default, the `nvcc` compiler is assumed to be accessible at the standard cuda toolkit install location `/usr/local/cuda`, however this is configurable via the command line using the `CUDADIR` flag like so:
```sh
make build/source.o CUDADIR=/path/to/cuda
```

If CUDA compilation is undesireable, it can be disabled by adding `NO_CUDA=1` to make commands.

```
make all NO_CUDA=1
```

## License
This repository is licensed for use under an MPL 2.0 derivative Open Source license.<br/>
The community is free to develop and change the code with the caveat that any changes must be for the benefit of the Mochimo cryptocurrency network (with a number of exclusions).<br/>
Please read the LICENSE for more details on limitations and restrictions.
