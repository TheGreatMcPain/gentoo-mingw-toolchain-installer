#!/bin/sh

stable="no"
graphite="no"
target=""

usage() {
    printf "Usage: $0 -h,-s,-g -t 32,64, or both\n\n"
    printf "    -h :    Print this help message.\n\n"
    printf "    -t <32,64,both> :   Which targets to build (required).\n\n"
    printf "    -s :    Build stable toolchain.\n\n"
    printf "    -g :    Enable 'graphite' USE for gcc.\n\n"
}

while getopts "sght:" params; do
    case $params in
        s)  stable="yes";;
        g)  graphite="yes";;
        h)  usage
            exit;;
        t)  target="$OPTARG";;
    esac
done

if [ "$1"x = "x" ]; then
    usage
    exit 1
fi

if [ $target = "32" ]; then
    TARGETS="i686-w64-mingw32"
elif [ $target = "64" ]; then
    TARGETS="x86_64-w64-mingw32"
elif [ $target = "both" ]; then
    TARGETS="x86_64-w64-mingw32 i686-w64-mingw32"
else
    echo "'$target' Is not a valid option."
fi

# build_toolchain target ("yes" for graphite) ("yes" for stable)
build_toolchain() {
    TARGET=$1

    GCC_GRAPHITE="no"
    CROSSDEV_OPTS=

    if [ "$2"x = "yes"x ]; then
        GCC_GRAPHITE="yes"
    fi

    if [ "$3"x = "yes"x ]; then
        CROSSDEV_OPTS="-S"
    fi

    # Create initial toolchain
    crossdev $CROSSDEV_OPTS -t $TARGET || exit 1

    # Disable sjlj for 32bit, but enable dwarf2.
    # (Extra performance for 32bit DXVK)
    if [ $TARGET = "i686-w64-mingw32" ]; then
        GCC_ENV="EXTRA_ECONF=\"--enable-threads=posix"
        GCC_ENV="$GCC_ENV --disable-sjlj-exceptions --with-dwarf2\""
    else
        GCC_ENV="EXTRA_ECONF=\"--enable-threads=posix\""
    fi

    # If GCC_GRAPHITE is 'yes' then enable the graphite USE for gcc
    if [ $GCC_GRAPHITE = "yes" ]; then
        GCC_USE="USE=\"graphite\""
    else
        GCC_USE=""
    fi

    # Enable 'libraries' for mingw64-runtime, and enable threading in gcc.
    crossdev $CROSSDEV_OPTS --lenv 'USE="libraries"' \
        --genv "$GCC_USE $GCC_ENV" --init-target -t $TARGET || exit 1

    # Modify mingw64-runtime's useflags in package.use to enable libraries.
    sed -i "s|-libraries|libraries|" /etc/portage/package.use/cross-$TARGET || exit 1

    # Rebuild mingw64-runtime with libraries USE
    emerge -1 --quiet-build y cross-$TARGET/mingw64-runtime || exit 1

    # Rebuild gcc with threading.
    emerge -1 --quiet-build y cross-$TARGET/gcc || exit 1
}

for x in $TARGETS; do
    build_toolchain $x $graphite $stable
done
