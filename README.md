# Sysrepo

[![GitHub license](https://img.shields.io/badge/license-Apache%20license%202.0-blue.svg)](https://github.com/sysrepo/sysrepo/blob/master/LICENSE)

Sysrepo is a [YANG](http://tools.ietf.org/html/rfc7950)-based configuration and operational state data store for Unix/Linux applications.

Applications can use sysrepo to store their configuration modeled by provided YANG model instead of using e.g. flat configuration files. Sysrepo will ensure data consistency of the data stored in the datastore and enforce data constraints defined by YANG model. Applications can currently use [C language API](inc/sysrepo.h) of sysrepo Client Library to access the configuration in the datastore, but the support for other programming languages is planed for later, too.

Sysrepo can be easily integrated with management agents such as [NETCONF](https://tools.ietf.org/html/rfc6241) or [RESTCONF](https://tools.ietf.org/html/rfc8040) servers, using the same client library API that applications use to access their configuration. As of now, sysrepo is integrated with the [Netopeer 2 NETCONF server](https://github.com/CESNET/Netopeer2). This means that applications that use sysrepo to store their configuration can automatically benefit from the ability to being controlled via NETCONF.

## Provided Features

* Ability to store / retrieve YANG-modeled data elements adressed by XPath
* Startup, running, candidate, and operational datastore support
* Data consistency and constraints enforcement according to YANG models
* No single point of failure design (is just a library)
* Full transaction and concurrency support
* Notifications of subscribed applications about the changes made in the datastore
* Commit verifiers (change verification by subscribed applications)
* Operational data support (publishing of application's state/configuration data to sysrepo)
* YANG 1.1 support
* Custom RPC, Event Notifications, YANG 1.1 Actions support
* Notification store & notification replay

## Requirements

### Build Requirements

* C compiler (gcc >= 4.8.4, clang >= 3.0, ...)
* cmake >= 2.8.12
* [libyang](https://github.com/CESNET/libyang)

#### Optional

* doxygen (for generating documentation)
* cmocka >= 1.0.0 (for tests only, see [Tests](#Tests))
* valgrind (for enhanced testing)

## Building

```
$ mkdir build; cd build
$ cmake ..
$ make
# make install
```

### Documentation

The library documentation can be generated directly from the source codes using
Doxygen tool:
```
$ make doc
$ google-chrome ../doc/html/index.html
```

### Useful CMake Options

#### Changing Compiler

Set `CC` variable:

```
$ CC=/usr/bin/clang cmake ..
```

#### Changing Install Path

To change the prefix where the library, headers and any other files are installed,
set `CMAKE_INSTALL_PREFIX` variable:
```
$ cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr ..
```

Default prefix is `/usr/local`.

#### Build Modes

There are two build modes:
* Release.
  This generates library for the production use without any debug information.
* Debug.
  This generates library with the debug information and disables optimization
  of the code.

The `Debug` mode is currently used as the default one. to switch to the
`Release` mode, enter at the command line:
```
$ cmake -D CMAKE_BUILD_TYPE:String="Release" ..
```

## Usage

All Sysrepo functions are available via the main header:

```C
#include <sysrepo.h>
```

To compile your program with libsysrepo, it is necessary to link it using the
following linker parameters:
```
-lsysrepo
```

Note, that it may be necessary to call `ldconfig(8)` after library installation and if the
library was installed into a non-standard path, the path to libyang must be specified to the
linker. To help with setting all the compiler's options, there is `libsysrepo.pc` file for
`pkg-config(1)` available in the source tree. The file is installed with the library.

## Examples

See [examples](examples) directory, which contains an example for basic API functions.

## Tests

libyang includes several tests built with [cmocka](https://cmocka.org/). The tests
can be found in `tests` subdirectory and they are designed for checking library
functionality after code changes.

The tests are by default built in the `Debug` build mode by running
```
$ make
```

In case of the `Release` mode, the tests are not built by default (it requires
additional dependency), but they can be enabled via cmake option:
```
$ cmake -DENABLE_BUILD_TESTS=ON ..
```

Note that if the necessary [cmocka](https://cmocka.org/) headers are not present
in the system include paths, tests are not available despite the build mode or
cmake's options.

Tests can be run by the make's `test` target:
```
$ make test
```