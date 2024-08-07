RISC-V Proxy Kernel and Boot Loader
=====================================

About
---------

The RISC-V Proxy Kernel, `pk`, is a lightweight application execution
environment that can host statically-linked RISC-V ELF binaries.  It is
designed to support tethered RISC-V implementations with limited I/O
capability and thus handles I/O-related system calls by proxying them to
a host computer.

This package also contains the Berkeley Boot Loader, `bbl`, which is a
supervisor execution environment for tethered RISC-V systems.  It is
designed to host the RISC-V Linux port.

Build Steps
---------------

We assume that the RISCV environment variable is set to the RISC-V tools
install path, and that the riscv-gnu-toolchain package is installed.
Please note that building the binaries directly inside the source
directory is not supported; you need to use a separate build directory.

    $ mkdir build
    $ cd build
    $ ../configure --prefix=$RISCV --host=riscv64-unknown-elf
    $ make
    $ make install

Alternatively, the GNU/Linux toolchain may be used to build this package,
by setting `--host=riscv64-unknown-linux-gnu`.

By default, 64-bit (RV64) versions of `pk` and `bbl` are built.  To
built 32-bit (RV32) versions, supply a `--with-arch=rv32i` flag to the
configure command.

The `install` step installs 64-bit build products into a directory
matching your host (e.g. `$RISCV/riscv64-unknown-elf`). 32-bit versions 
are installed into a directory matching a 32-bit version of your host (e.g.
`$RISCV/riscv32-unknown-elf`).

OpenBSD Build Steps
-------------------

Install the riscv-gnu-toolchain, and follow generic build steps.

    # pkg_add riscv-elf-binutils riscv-elf-gcc riscv-elf-newlib

Troubleshooting
---------------

In *Build Steps*, it is stated:

> We assume that the RISCV environment variable is set to the RISC-V tools
install path, and that the riscv-gnu-toolchain package is installed


- Make sure RISC-V tools are installed. You can find them [here](https://github.com/riscv-collab/riscv-gnu-toolchain).
    - By default, they are installed in `/opt/riscv/`. **We assume this is the case**, otherwise
    modify the following steps appropriately.
- Set the environment variable (perhaps in your shell's `.rc` file) as such: `export RISCV="/opt/riscv"`
    - You can also try adding `/opt/riscv/bin/` to `$PATH`.
- In case of encountering the following error: <br>
``Error: unrecognized opcode `fence.i', extension `zifencei' required``:
    - **before** the `../configure ...`, run this command: <br> 
    ``export PLATFORM_RISCV_ISA=rv64imafdc_zifencei``.
        - if you did this in a wrong order, run `make clean` (also try this in case something doesn't work).
    - Now run this modified `../configure ...` command: <br>
    ``../configure --prefix=$RISCV --host=riscv64-unknown-elf --with-arch=rv64gc_zifencei``.
