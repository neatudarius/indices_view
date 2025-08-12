# beman.indices_view: A range adaptor that generates a sequence of integers from $0$ to $n - 1$

<!--
SPDX-License-Identifier: Apache-2.0 WITH LLVM-exception
-->

<!-- markdownlint-disable-next-line line-length -->
![Library Status](https://raw.githubusercontent.com/bemanproject/beman/refs/heads/main/images/badges/beman_badge-beman_library_production_ready_stable_api.svg)
![Continuous Integration Tests](https://github.com/bemanproject/indices_view/actions/workflows/ci_tests.yml/badge.svg)
![Lint Check (pre-commit)](https://github.com/bemanproject/indices_view/actions/workflows/pre-commit.yml/badge.svg)
![Target Standard](https://github.com/bemanproject/beman/blob/main/images/badges/cpp26.svg)

**Implements**: `std::views::indices` proposed in [Add std::views::indices(n) (P3060R2)](https://wg21.link/P3060R2).

**Status**: [Production ready. Stable API.](https://github.com/bemanproject/beman/blob/main/docs/beman_library_maturity_model.md#production-ready-stable-api)

## Usage

```cpp
#include <iostream>
#include <vector>

#include <beman/indices_view/indices.hpp>

namespace biv = beman::indices_view;

// Example given in the paper for `std::views::indices`, adapted to work with C++20.
int main() {
    std::vector rng(5, 0);

    std::cout << "[";
    bool first = true;
    for (auto i : biv::indices(std::ranges::size(rng))) {
        if (!first) {
            std::cout << ", ";
        }
        std::cout << i;
        first = false;
    }
    std::cout << "]\n";

    return 0;
}

```

Full runnable examples can be found in [`examples/`](examples/).

## Dependencies

### Build Environment

This project requires at least the following to build:

* C++20
* CMake 3.25
* (Test Only) GoogleTest

You can disable building tests by setting the CMake option
[`BEMAN_INDICES_VIEW_BUILD_TESTS`](#beman_indices_view_build_tests) to `OFF`
when configuring the project.

### Supported Platforms

This project officially supports:

* GNU GCC Compiler \[version 12-14\]
* LLVM Clang++ Compiler \[version 17-20\]
* AppleClang compiler on Mac OS
* MSVC compiler on Windows

> [!NOTE]
>
> Versions outside of this range would likely work as well,
> especially if you're using a version above the given range
> (e.g. HEAD/nightly).
> These development environments are verified using our CI configuration.

## Development

### Develop using GitHub Codespace

This project supports [GitHub Codespace](https://github.com/features/codespaces)
via [Development Containers](https://containers.dev/),
which allows rapid development and instant hacking in your browser.
We recommend you using GitHub codespace to explore this project as this
requires minimal setup.

You can create a codespace for this project by clicking this badge:

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/bemanproject/exemplar)

For more detailed documentation regarding creating and developing inside of
GitHub codespaces, please reference [this doc](https://docs.github.com/en/codespaces/).

> [!NOTE]
>
> The codespace container may take up to 5 minutes to build and spin-up,
> this is normal as we need to build a custom docker container to setup
> an environment appropriate for beman projects.

### Develop locally on your machines

<details>
<summary> For Linux based systems </summary>

Beman libraries require [recent versions of CMake](#build-environment),
we advise you to download CMake directly from [CMake's website](https://cmake.org/download/)
or install it via the [Kitware apt library](https://apt.kitware.com/).

A [supported compiler](#supported-platforms) should be available from your package manager.
Alternatively you could use an install script from official compiler vendors.

Here is an example of how to install the latest stable version of clang
as per [the official LLVM install guide](https://apt.llvm.org/).

```bash
bash -c "$(wget -O - https://apt.llvm.org/llvm.sh)"
```

If the included test suite is being built and run, a GoogleTest library will be
required. Here is an example of installing GoogleTest on a Debian-based Linux
environment:

```bash
apt install libgtest-dev
```

The precise command and package name will vary depending on the Linux OS you are
using. Be sure to consult documentation and the package repository for the system
you are using.

</details>

<details>
<summary> For MacOS based systems </summary>

Beman libraries require [recent versions of CMake](#build-environment).
You can use [`Homebrew`](https://brew.sh/) to install the latest major version of CMake.

```bash
brew install cmake
```

A [supported compiler](#supported-platforms) is also available from brew.

For example, you can install the latest major release of Clang as:

```bash
brew install llvm
```

</details>

<details>
<summary> For Windows </summary>

To build Beman libraries, you will need the MSVC compiler. MSVC can be obtained
by installing Visual Studio; the free Visual Studio 2022 Community Edition can
be downloaded from
[Microsoft](https://visualstudio.microsoft.com/vs/community/).

After Visual Studio has been installed, you can launch "Developer PowerShell for
VS 2022" by typing it into Windows search bar. This shell environment will
provide CMake, Ninja, and MSVC, allowing you to build the library and run the
tests.

Note that you will need to use FetchContent to build GoogleTest. To do so,
please see the instructions in the "Build GoogleTest dependency from github.com"
dropdown in the [Project specific configure
arguments](#project-specific-configure-arguments) section.

</details>

### Configure and Build the Project Using CMake Presets

This project recommends using [CMake Presets](https://cmake.org/cmake/help/latest/manual/cmake-presets.7.html)
to configure, build and test the project.
Appropriate presets for major compilers have been included by default.
You can use `cmake --list-presets` to see all available presets.

Here is an example to invoke the `gcc-debug` preset.

```shell
cmake --workflow --preset gcc-debug
```

Generally, there are two kinds of presets, `debug` and `release`.

The `debug` presets are designed to aid development, so it has debugging
instrumentation enabled and as many sanitizers turned on as possible.

> [!NOTE]
>
> The set of sanitizer supports are different across compilers.
> You can checkout the exact set of compiler arguments by looking at the toolchain
> files under the [`cmake`](cmake/) directory.

The `release` presets are designed for use in production environments,
thus they have the highest optimization turned on (e.g. `O3`).

### Configure and Build Manually

While [CMake Presets](#configure-and-build-the-project-using-cmake-presets) are
convenient, you might want to set different configuration or compiler arguments
than any provided preset supports.

To configure, build and test the project with extra arguments,
you can run this set of commands.

```bash
cmake -B build -S . -DCMAKE_CXX_STANDARD=20 # Your extra arguments here.
cmake --build build
ctest --test-dir build
```

> [!IMPORTANT]
>
> Beman projects are
> [passive projects](https://github.com/bemanproject/beman/blob/main/docs/beman_standard.md#cmake),
> therefore,
> you will need to specify the C++ version via `CMAKE_CXX_STANDARD`
> when manually configuring the project.

### Finding and Fetching GTest from GitHub

If you do not have GoogleTest installed on your development system, you may
optionally configure this project to download a known-compatible release of
GoogleTest from source and build it as well.

Example commands:

```shell
cmake -B build -S . \
    -DCMAKE_PROJECT_TOP_LEVEL_INCLUDES=./infra/cmake/use-fetch-content.cmake \
    -DCMAKE_CXX_STANDARD=20
cmake --build build --target all
cmake --build build --target test
```

The precise version of GoogleTest that will be used is maintained in
`./lockfile.json`.

### Project specific configure arguments

When configuring the project manually,
you can pass an array of project specific CMake configs to customize your build.

Project specific options are prefixed with `BEMAN_INDICES_VIEW`.
You can see the list of available options with:

```bash
cmake -LH | grep "BEMAN_INDICES_VIEW" -C 2
```

<details>

<summary> Details of CMake arguments. </summary>

#### `BEMAN_INDICES_VIEW_BUILD_TESTS`

Enable building tests and test infrastructure. Default: ON.
Values: { ON, OFF }.

You can configure the project to have this option turned off via:

```bash
cmake -B build -S . -DCMAKE_CXX_STANDARD=20 -DBEMAN_INDICES_VIEW_BUILD_TESTS=OFF
```

> [!TIP]
> Because this project requires Google Tests as part of its development
> dependency,
> disable building tests avoids the project from pulling Google Tests from
> GitHub.

#### `BEMAN_INDICES_VIEW_BUILD_EXAMPLES`

Enable building examples. Default: ON. Values: { ON, OFF }.

</details>

## Integrate beman.indices_view into your project

To use `beman.indices_view` in your C++ project,
include an appropriate `beman.indices_view` header from your source code.

```c++
#include <beman/indices_view/indices.hpp>
```

> [!NOTE]
>
> `beman.indices_view` headers are to be included with the `beman/indices_view/` directories prefixed.
> It is not supported to alter include search paths to spell the include target another way. For instance,
> `#include <indices.hpp>` is not a supported interface.

How you will link your project against `beman.indices_view` will depend on your build system.
CMake instructions are provided in following sections.

### Linking your project to beman.indices_view with CMake

For CMake based projects,
you will need to use the `beman.indices_view` CMake module
to define the `beman::indices_view` CMake target:

```cmake
find_package(beman.indices_view REQUIRED)
```

You will also need to add `beman::indices_view` to the link libraries of
any libraries or executables that include beman.indices_view's header file.

```cmake
target_link_libraries(yourlib PUBLIC beman::indices_view)
```

### Produce beman.indices_view static library locally

You can include indices_view's headers locally
by producing a static `libbeman.indices_view.a` library.

```bash
cmake --workflow --preset gcc-release
cmake --install build/gcc-release --prefix /opt/beman.indices_view
```

This will generate such directory structure at `/opt/beman.indices_view`.

```txt
/opt/beman.indices_view
├── include
│   └── beman
│       └── indices_view
│           └── indices.hpp
└── lib
    └── libbeman.indices_view.a
```

## Contributing

Please do!
You encourage you to checkout our [contributor's guide](docs/README.md).
Issues and pull requests are appreciated.
