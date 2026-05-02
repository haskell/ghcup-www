# System requirements

## Linux Debian

### Generic

The following distro packages are required: `build-essential curl libffi-dev libffi8 libgmp-dev libgmp10 libncurses-dev libncurses6 libtinfo6 pkg-config`

### Version >= 11 && < 12

The following distro packages are required: `build-essential curl libffi-dev libffi7 libgmp-dev libgmp10 libncurses-dev libncurses5 libtinfo5 pkg-config`

### Version >= 12 && < 13

The following distro packages are required: `build-essential curl libffi-dev libffi8 libgmp-dev libgmp10 libncurses-dev libncurses5 libtinfo5 pkg-config`

### Version >= 13

The following distro packages are required: `build-essential curl libffi-dev libffi8 libgmp-dev libgmp10 libncurses-dev libncurses6 libtinfo6 pkg-config`

## Linux Ubuntu

### Generic

The following distro packages are required: `build-essential curl libffi-dev libffi6 libgmp-dev libgmp10 libncurses-dev libncurses5 libtinfo5`

### Version >= 20.04 && < 20.10

The following distro packages are required: `build-essential curl libffi-dev libffi7 libgmp-dev libgmp10 libncurses-dev libncurses5 libtinfo5`

### Version >= 20.10 && < 23

The following distro packages are required: `build-essential curl libffi-dev libffi8ubuntu1 libgmp-dev libgmp10 libncurses-dev libncurses5 libtinfo5`

### Version >= 23

The following distro packages are required: `build-essential curl libffi-dev libffi8ubuntu1 libgmp-dev libgmp10 libncurses-dev`

## Linux Fedora

### Generic

The following distro packages are required: `gcc gcc-c++ gmp gmp-devel make ncurses ncurses-compat-libs xz perl`


## Linux CentOS

### Generic

The following distro packages are required: `gcc gcc-c++ gmp gmp-devel make ncurses ncurses-compat-libs xz perl`

### Version >= 7 && < 8

The following distro packages are required: `gcc gcc-c++ gmp gmp-devel make ncurses xz perl`


## Linux Alpine

### Generic

The following distro packages are required: `binutils-gold curl gcc g++ gmp-dev libc-dev libffi-dev make musl-dev ncurses-dev perl tar xz`


## Linux (generic)

### Generic

You need the following packages: curl g++ gcc gmp make ncurses realpath xz-utils. Consult your distro documentation on the exact names of those packages.

## Darwin

### Generic

On OS X, in the course of running ghcup you will be given a dialog box to install the command line tools. Accept and the requirements will be installed for you. You will then need to run the command again.
On Darwin M1 you might also need a working llvm installed (e.g. via brew) and have the toolchain exposed in PATH.

## FreeBSD

### Generic

The following distro packages are required: `curl gcc gmp gmake ncurses perl5 libffi libiconv`


## Windows

### Generic

On Windows, msys2 should already have been set up during the installation, so most users should just proceed. If you are installing manually, make sure to have a working mingw64 toolchain and shell.

## WSL2

### Generic

Follow instructions for your Linux distro.
