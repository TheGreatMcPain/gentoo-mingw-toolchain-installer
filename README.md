# A Mingw64 toolchain script for Gentoo (WIP)

With the removal of the ability to build DXVK with winelib, and with Wine slowly integrating PE DLLs.  A Mingw64 toolchain is starting to become a requirement to build DXVK, and possibly future Wine versions.

Unfortunatly for Gentoo users getting a working Mingw64 cross-compiler with pthread support is a pretty complex process. This script will try to help make this process a little easier.

### The script will do the following.

* Create mingw toolchains using `crossdev`

* Enable the `libraries` useflag for `mingw64-runtime`,
and set `--enable-threads=posix` for `gcc` also using `crossdev`.

* Rebuild `mingw64-runtime`, and `gcc`.

## Usage

| Argument | Usage |
|:--------:| ----- |
| -h | Print help message |
| -s | Create stable versions of the toolchain. (default is latest available) |
| -g | enable the `graphite` useflag for `gcc`. (For those using GentooLTO.) |

## Binutils 2.35.1 patch

There's currently a bug with dwarf2 that causes the 32bit version of dxvk to fail to build.

See: [Gentoo bug #745372](https://bugs.gentoo.org/745372)

The `binutils-2.35.1-fix-dxvk-build.patch` patch should fix it.

Just create these directories in `/etc/portage/patches`

```
# cd /etc/portage/patches

# mkdir -pv cross-x86_64-w64-mingw32/binutils-2.35.1

# ln -sv cross-x86_64-w64-mingw32 cross-i686-w64-mingw32

# cp -v /path/to/binutils-2.35.1-fix-dxvk-build.patch cross-x86_64-w64-mingw32/binutils-2.35.1/
```

Then rebuild mingw32's binutils.

```
# emerge -1va cross-{x86_64,i686}-w64-mingw32/binutils
```
