---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
# title: "Quick Start Guide"
---

The recommended way to build the PACStack LLVM compiler using our buildhelper
scripts available at:  
[https://github.com/pacstack/buildhelper](https://github.com/pacstack/buildhelper)

The buildhelper scripts requires a Linux host with:
- Linux kernel with [`binfmt_misc`](https://www.kernel.org/doc/html/latest/admin-guide/binfmt-misc.html) support,
- [Git](https://git-scm.com/) version 2.5 or above, and 
- [Singularity](https://sylabs.io/singularity/) version 3.5 or above installed.

Ubuntu and other Debian derivative users can install the `binfmt-support` and
`git` packages via `apt`:

    $ apt install binfmt-support git

To install Singularity we recommend following the **Quick Start** guide at:  
[https://sylabs.io/guides/3.5/user-guide/quick_start.html](https://sylabs.io/guides/3.5/user-guide/quick_start.html)

## Building the Singularity buildhost container

Once dependencies have been installed, clone the buildhelper repository and
build the Singularity container for performing builds:

    $ git clone https://github.com/pacstack/buildhelper
    $ cd ./buildhelper
    $ sudo ./build-image.sh

**Note:** `build-image.sh` must be run with as root as Singularity requires root
privileges when building containers from recipes. If your Singularity
installation has the [fakeroot feature](https://sylabs.io/guides/3.5/user-guide/fakeroot.html) enabled, then `build-image.sh` can be run as a regular user.
In this case pass the `--fakeroot` option to `build-image.sh`:

    $ ./build-image.sh --fakeroot

This feature requires at minimum kernel version >=3.8, but >=3.18 is recommended
and that user and group mappings have been setup using `singularity config fakeroot`.
Please see the [Singularity documentation for more information](singularity config fakeroot).

The `build-image.sh` script produces `llvm-toolchain-buildhost.sif` in the current working directory.

**Note:** the container build process requires ≈ 600MB of free space in the `/tmp` filesystem.

### Registering `binfmt_misc` binary formats for AArch64

To run 64-bit ARM binaries directly within the container the AArch64 binary
formats must be registered with `binfmt_misc` by writing to the `/proc/sys/fs/binfmt_misc` filesystem.
This is done using the `scripts/qemu-binfmt-conf.sh` script.
As the `/proc/sys/fs/binfmt_misc` are common between host and inside of
container, the register script must be run with root privileges on the host:

    $ sudo scripts/qemu-binfmt-conf.sh   

Ubuntu and other Debian derivative users can make the changes persist between
reboots by specifying the `--systemd ALL` option to `qemu-binfmt-conf`:

    $ sudo scripts/qemu-binfmt-conf.sh  --systemd ALL

**Note:** This step is not necessary if the `qemu-user-static` package is
already installed on the host. 

### Building LLVM

LLVM builds are started by invoking `build.sh` and passing it a corresponding manifest file.

Currently the following manifests are supported:

- PACStack LLVM 9.0.1 cross compiler for Aarch64 ( _Release_ ): `x86_64.aarch64-linux-gnu-pacstack-llvm-9.0.1-release-manifest.txt`
- PACStack LLVM 9.0.1 cross compiler for Aarch64 ( _Debug_ ):   `x86_64.aarch64-linux-gnu-pacstack-llvm-9.0.1-debug-manifest.txt`

Most users will prefer _Release_ builds, as they are faster to compile and have
better performance. To build PACStack LLVM 9.0.1 in _Release_ configuration use
the following command:

    $ ./build.sh manifest/x86_64.aarch64-linux-gnu-pacstack-llvm-9.0.1-release-manifest.txt

This will download all necessary dependencies, build LLVM and automatically run
regression tests on binaries produced by the new compiler. Once the build has
completed successfully the PACStack toolchain can be found in
`llvm-9.0.1-pacstack-release/destdir/aarch64-linux-gnu/`.

### Cross compiling with PACStack LLVM

PACStack uses the ARMv8.3-a PAuth extension and supports only AArch64 targets
with PAuth extension (e.g., `-march=armv8.3-a`). Consequently the binaries to be
instrumented have to be (cross-)compiled for AArch64 using the PACStack LLVM
toolchain.

PACStack instrumentation is enabled using the following compiler options:

- PACStack (full)  
  `-mllvm -pacstack=full`
- PACStack-nomask  
  `-mllvm -pacstack=nomask`
- PACStack using PA-analogue instead of PAuth instructions  
  `-mllvm -pacstack=full -mllvm -aarch64-pacstack-dummy-pa`
- PACStack-nomask using PA-analogue instead of PAuth instructions  
  `-mllvm -pacstack=nomask -mllvm -aarch64-pacstack-dummy-pa`

Note that since LLVM project does not have implementations of all the parts of
toolchain and library dependencies it must be combined with a GCC cross compile
toolchain to provide missing components. The buildhelper scripts obtain a copy
of Linaro toolchain (available at [https://www.linaro.org/downloads/](https://www.linaro.org/downloads/)) and
installs it at `llvm-9.0.1-pacstack-release/sysroots/aarch64-linux-gnu/`.

Source code packages that provides a configuration scripts produced using
Autoconf can generally be cross compiled by invoking `./configure` as follows:

<pre>
$ ./configure --host=aarch64-unknown-linux-gnu \
    CC=<i>/path/to/pacstack/llvm/toolchain</i>/bin/clang \
    LDSHARED=<i>/path/to/pacstack/llvm/toolchain</i>/bin/clang \
    CFLAGS="--target=aarch64-linux-gnu --sysroot=<i>/path/to/linaro/sysroot</i> --gcc-toolchain=<i>/path/to/linaro/gcc</i>" \
    LDFLAGS="-Wl,--dynamic-linker=<i>/path/to/linaro/sysroot</i>/lib/ld-linux-aarch64.so.1"
</pre>

Instrumented binaries can be run using either ARM's [Armv8-A Architecture Fixed
Virtual Platform (FVP)](https://developer.arm.com/tools-and-software/simulation-models/fixed-virtual-platforms) simulation models, or the [QEMU](https://www.qemu.org/) machine emulator version
4.0 or newer. The instructions here assume QEMU and that binary formats for the
Aarch64 binaries have been registered with Linux so that it will be possible to
run foreign binaries directly. On Ubuntu and other Debian-based distributions
this can be achieved by installing the `qemu-user-static` deb package.

**Note:** Binaries linked against a sysroot at a non-standard location require `LD_LIBRARY_PATH` to be set appropriately:  
<pre>
$ LD_LIBRARY_PATH=<i>/path/to/linaro/sysroot</i>/lib
</pre>
