Build notes
===========


## libuv

The contents of the libuv/ directory are the canonical libuv sources, with the following changes.

****

The libuv sources contain unnamed structs, which result in warnings on MinGW's GCC. This in turn causes WARNINGS in R CMD check on Windows. Commit f40b733 converted them to named structs.

*****

The Makefile.am file is modified for Solaris support. This is the original line:

```
libuv_la_CFLAGS += -D__EXTENSIONS__ -D_XOPEN_SOURCE=500
```

It has `-DSUNOS_NO_IFADDRS` added to it. See [here](https://github.com/libuv/libuv/issues/1458) for more information.

```
libuv_la_CFLAGS += -D__EXTENSIONS__ -D_XOPEN_SOURCE=500 -DSUNOS_NO_IFADDRS
```

*****

After modifying Makefile.am, run `./autogen.sh`. This requires automake and libtool, and generates the `configure` script, along with a number of other related files. These generated files are checked into the repository so that other systems to not need automake and libtool to build libuv.

The file `libuv/m4/lt~obsolete.m4` (generated by autogen.sh) is renamed to `lt_obsolete.m4` because the filename with the `~` causes problems with `R CMD check`. In the Makevars file, it gets copied to `lt~obsolete.m4` so that it's present during the build process.

In Makevars, before running `./configure`, it updates timestamps for `Makefile.in`, `aclocal.m4`, `configure`, and `m4/lt~obsolete.m4`. If this is not done, then the configure script may generate a Makefile which tries to find `aclocal-1.15` and other autotools-related programs. It decides whether to do this based on file timestamps. See this [SO question](https://stackoverflow.com/questions/33278928/how-to-overcome-aclocal-1-15-is-missing-on-your-system-warning-when-compilin) for more information.

The following generated files are checked into the repository:

```
configure
src/Makevars
src/libuv/Makefile.in
src/libuv/aclocal.m4
src/libuv/ar-lib
src/libuv/compile
src/libuv/config.guess
src/libuv/config.sub
src/libuv/configure
src/libuv/depcomp
src/libuv/install-sh
src/libuv/ltmain.sh
src/libuv/m4/libtool.m4
src/libuv/m4/libuv-extra-automake-flags.m4
src/libuv/m4/lt_obsolete.m4    * NOTE: this was renamed
src/libuv/m4/ltoptions.m4
src/libuv/m4/ltsugar.m4
src/libuv/m4/ltversion.m4
src/libuv/missing
```
