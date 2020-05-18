# A Mingw64 toolchain script for Gentoo (WIP)

With the official removal of the ability to build DXVK with winelib, and with Wine slowly integrating PE DLLs.  A Mingw64 toolchain is starting to become a requirement to build DXVK, and possibly future Wine versions.

Unfortunatly for Gentoo users getting a working Mingw64 cross-compiler with pthread support is a pretty complex process. This script will try to help make this process a little easier.

The script will do the following.

* Remove files that were previously created by this script.

* Use crossdev to create x86\_64-w64-mingw32, and i686-w64-mingw32 toolchains.

* Add pthread, and dwarf2, EXTRA\_ECONF options to /etc/portage/env.

* Enable libraries useflag for mingw64-runtime.

* Apply /etc/portage/env settings to mingw cross-{x86\_64-w64,i686}-mingw32/gcc packages.

* Re-build mingw64-runtime with the libraries useflag

* Re-build cross-{x86\_64-w64,i686}-mingw32/gcc with the EXTRA\_ECONF settings.
