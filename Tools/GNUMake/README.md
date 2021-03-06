The build system requires GNU make >= 3.81 and python >= 2.7.

Typically an application will have its own `GNUmakefile`.  (See
`Tutorials/HelloWorld_C/` for a simple example, or
`Tutorials/AMR_Adv_C_v2/Exec/SingleVortex/` for a lightly more
complicated example.)  `Make.defs` is included near the beginning, and
`Make.rules` is included in the end.  Depending the need,
`GNUmakefile` includes a number of
`$(AMREX_HOME)/Src/xxx/Make.package`, where `xxx` is `Base` etc.
These `Make.package` files add sources to the make system.  An
application also has its own `Make.package`.  The make variables for
the sources are:

* `CEXE_sources` for C++ source files with `.cpp` extension.
* `CEXE_headers` for C++ headers with `.h` or `.H` extension.
* `cEXE_sources` for C source files with `.c` extension.
* `cEXE_headers` for C headers with `.h` or `.H` extension.
* `f90EXE_sources` for free format Fortran sources with `.f90` extension.
* `F90EXE_sources` for free format Fortran sources with `.F90` extension requiring preprocessing.
* `fEXE_sources` for fixed format Fortran sources with `.f` extension.
* `FEXE_sources` for fixed format Fortran sources with `.F` extension requiring preprocessing.

The variable `AMREX_HOME` points to the path to AMReX.  It must be
set either in the makefile or via the shell's environment variable.

Typically one types `make` to build an executable.  Other useful
commands include:

* `make cleanconfig` removes the executable, `.o` files, and the resulting files of preprocessing.
* `make clean` removes all files generated by the make system.
* `make help` shows the rules for compilation.
* `make print-xxx` shows the value of `xxx`.  This is very useful for
  debugging.
* `make file_locations` shows the path of each file.
* `make tags` and `make TAGS` generate tag files using `ctags` and `etags`, respectively.

The `Make.defs` includes the following files in the listed order:

* `Make.machines`: This file defines three variables, `which_site`,
  `which_computer`, and `host_name`.  For example, `which_site` is
  `nersc` and `which_computer` is `cori` on NERSC's Cori system.  If
  the machine is unknown to the build system, `which_site` and
  `which_computer` are defined as `unknown`.  The `host_name` variable
  is set to the environment variable `HOSTNAME` if defined.
  Otherwise, it is set to the environment variable `HOST`.  If neither
  `HOSTNAME` nor `HOST` is defined, `host_name` is defined to the
  result of `hostname -f` shell command.

* `comps/xxx.mak`: Here `xxx` is either `gnu`, or `intel`, or `pgi`,
  or `cray` depending on the make variable `COMP`, which must be set
  either in `GNUmakefile` or via command line (e.g., `make COMP=gNu`).
  Note that the compiler name is case insensitive.  This file defines
  compiler specific variables.   Usually it must define:

  * `CXX`: C++ compiler.
  * `CC`: C compiler.
  * `FC`: Fortran compiler for fixed format files.
  * `F90`: Fortran compiler for free format files.
  * `CXXFLAGS`: Flags for C++ compiler.
  * `CFLAGS`: Flags for C compiler.
  * `FFLAGS`: Flags for fixed format Fortran compiler.
  * `F90FLAGS`: Flags for free format Fortran compiler.
  * `DEFINES`: This defines the macros passed the compilers.

  Type `make help` for more details on the compilation rules.

* `tools/Make.xxx`: Here `xxx` is an optional tool such as VTune, if
  it is used.

* `packages/Make.xxx`: Here `xxx` is an optional external package such
  as HPGMG, if it is used.

* `sites/Make.xxx`: Here which file to include is resolved as follows.
  If a file `$(AMREX_HOME)/Tools/GNUMake/sites/Make.$(host_name)`
  exists, it is included.  Otherwise,
  `$(AMREX_HOME)/Tools/GNUMake/sites/Make.$(which_site)` is included.
  Note that both `host_name` and `which_site` are defined in
  `Make.machines`.  This file is used for site specific setup.
  Typically this is used for MPI related setup.  The file
  `sites/Make.unknown` contains a generic setup for using MPI
  wrappers.  Note that when MPI is used Fortran 90 MPI library may
  also need to be linked to because it is used in parts of AMReX.

* `Make.local`: This file is not in the AMReX repository.  It is the
  last file included in `Make.defs`, if it exists.  It is meant for a
  purely local setup that can override the setup in previously
  included files.  See `Make.local.template` for examples.

The build system uses the `vpath` directive to specify a list of
directories to be searched for files.  The list of directories is
defined in the `VPATH_LOCATIONS` variables.  An application code
should add its own source directories to the variable.  The build system
also implicitly adds the current directory to the top of the list.  So
a file in the current directory will hide a file with the same name
but stored in different a directory.

The `INCLUDE_LOCATIONS` variable specifies for the compilers a list
directories to be searched for header files.  Each directory specified
in `INCLUDE_LOCATIONS` will be passed to the compilers with a `-I`
prefix.  An application code should add its own header file
directories to the variable.

The C++ compiler specified in the variable `CXX` is used for linking.
The variables `LIBRARIES` and `XTRALIBS` are used to form a variable
called `libraries`, which is used to specify the libraries to be
linked.  The locations for searching the libraries are specified in
variable `LIBRARY_LOCATIONS`.  Each directory in `LIBRARY_LOCATIONS`
will be prefixed with `-L` and passed to the compilers.

See User's Guide for more information on various build options (e.g.,
`USE_MPI`).
