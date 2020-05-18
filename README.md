# A Mingw64 toolchain script for Gentoo (WIP)

With the official removal of the ability to build DXVK with winelib, and with Wine slowly integrating PE DLLs.  A Mingw64 toolchain is starting to become a requirement to build DXVK, and possibly future Wine versions.

Unfortunatly for Gentoo users getting a working Mingw64 cross-compiler with pthread support is a pretty complex process. This script will try to help make this process a little easier.

The script will do the following.

* Remove files that were previously created by this script.

* Run `crossdev -S --libc "~7.0.0" -t x86_64-w64-mingw32`

* Run `crossdev -S --libc "~7.0.0" -t i686-w64-mingw32`

* Run `echo "EXTRA_ECONF=\"--enable-threads=posix\"" > /etc/portage/env/mingw_pthreads`

* Run `echo "EXTRA_ECONF=\"--enable-threads=posix --disable-sjlj-exceptions --with-dwarf2\"" > /etc/portage/env/mingw_pthreads_dwarf2`

* Run `echo -e "cross-x86_64-w64-mingw32/mingw64-runtime libraries\ncross-i686-w64-mingw32/mingw64-runtime libraries" > /etc/portage/package.use/mingw64-runtime-libraries`

* Run `echo -e "cross-x86_64-w64-mingw32/gcc mingw_pthreads\ncross-x86_64-w64-mingw32/gcc mingw_pthreads_dwarf2" > /etc/portage/package.env/mingw-gcc`

* Run `emerge -1v cross-x86_64-w64-mingw32/mingw64-runtime cross-i686-w64-mingw32/mingw64-runtime`

* Run `emerge -1v cross-x86_64-w64-mingw32/gcc cross-i686-w64-mingw32/gcc`
