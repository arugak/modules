.. _INSTALL:

Installing Modules on Unix
==========================

This document is an overview of building and installing Modules on a Unix
system.


Requirements
------------

Modules consists of one Tcl script so to run it from a user shell the
only requirement is to have a working version of ``tclsh`` (version
8.4 or later) available on your system. ``tclsh`` is a part of Tcl
(http://www.tcl.tk/software/tcltk/).

To install Modules from a distribution tarball or a clone of the git
repository, a build step is there to adapt the initialization scripts to your
configuration and create the documentation files. This build step requires
the tools to be found on your system:

* bash
* make
* sed
* grep
* cut
* runtest

When also installing the bundled compatibility version of Modules (enabled
by default), these additional tools are needed:

* autoconf
* automake
* autopoint
* gcc
* tcl-devel >= 8.4

When installing from a distribution tarball, documentation is pre-built and
does not require additional software. When installing from a clone of the git
repository, document has to be built and the following tools are required:

* sphinx >= 1.0


Installation instructions
-------------------------

The simplest way to build and install Modules is::

    $ ./configure
    $ make
    $ make install

Some explanation, step by step:

1. ``cd`` to the directory containing the package's source code. Your system
   must have the above requirements installed to properly build scripts,
   compatibility version of Modules if enabled, and documentation if build
   occurs from a clone of the git repository.

2. Type ``./configure`` to adapt the installation for your system. At this
   step you can choose the installation paths and the features you want to
   enable in the initialization scripts (see `Build and installation options`_
   section below for a complete overview of the available options)

3. Type ``make`` to adapt scripts to the configuration, build compatibility
   version if enabled and build documentation if working from git repository.

4. Optionally, type ``make test`` to run the test suite.

5. Type ``make install`` to install modulecmd.tcl, initialization scripts,
   compatibility version if built and documentation.

6. Optionally, type ``make testinstall`` to run the installation test suite.

7. You can remove the built files from the source code directory by typing
   ``make clean``. To also remove the files that ``configure`` created, type
   ``make distclean``.

A default installation process like described above will install Modules
under ``/usr/local/Modules``. You can change this with the ``--prefix``
option. By default, ``/usr/local/Modules/modulefiles`` will be setup as
the default directory containing modulefiles. ``--modulefilesdir`` option
enables to change this directory location. For example::

    $ ./configure --prefix=/usr/share/Modules \
                  --modulefilesdir=/etc/modulefiles

See `Build and installation options`_ section to discover all ``./configure``
option available.

.. note:: GNU Make is excepted to be used for this build and installation
   process. On non-Linux systems, the ``gmake`` should be called instead of
   ``make``.


Configuration
-------------

Once installed you should review and adapt the configuration to make it fit
your needs. The following steps are provided for example. They are not
necessarily mandatory as it depends of the kind of setup you want to achieve.

1. Tune the initialization scripts. Review of these scripts is highly
   encouraged as you may add or adapt specific stuff to get Modules
   initialized the way you want.

2. Enable Modules initialization at shell startup. An easy way to get module
   function defined and its associated configuration setup at shell startup
   is to make the initialization scripts part of the system-wide environment
   setup in ``/etc/profile.d``. To do so, make a link in this directory to the
   profile scripts that can be found in your Modules installation init
   directory::

       $ ln -s PREFIX/init/profile.sh /etc/profile.d/modules.sh
       $ ln -s PREFIX/init/profile.csh /etc/profile.d/modules.csh

   These profile scripts will automatically adapt to the kind of ``sh`` or
   ``csh`` shell you are running.

   Another approach may be to get the Modules initialization script sourced
   from the shell configuration startup file. For instance following line
   could be added to the end of the ``~/.bashrc`` file if running Bash shell::

       source PREFIX/init/bash

   Beware that shells have multiple ways to initialize depending if they are
   a login shell or not and if they are launched in interactive mode or not.

3. Define module paths to enable by default. Edit ``modulerc`` configuration
   file or ``.modulespath`` if you have chosen ``--enable-dotmodulespath`` at
   configure time. Add there all the modulefile directories you want to
   activate by default at Modules initialization time.

   If you use ``.modulespath`` configuration file, add one line mentioning
   each modulefile directory::

       /path/to/regular/modulefiles
       /path/to/other/modulefiles

   If you use ``modulerc`` configuration file, add one line mentioning each
   modulefile directory prefixed by the ``module use`` command::

       module use /path/to/regular/modulefiles
       module use /path/to/other/modulefiles

4. Define modulefiles to load by default. Edit ``modulerc`` configuration file
   (modulefiles to load cannot be specified in ``.modulespath`` file). Add
   there all the modulefiles you want to load by default at Modules
   initialization time.

   Add one line mentioning each modulefile to load prefixed by the
   ``module load`` command::

       module load foo
       module load bar

   In fact you can add to the ``modulerc`` configuration file any kind of
   supported module command.

If you go through the above steps you should have a valid setup tuned to your
needs. After that you still have to write modulefiles to get something to
load and unload in your newly configured Modules setup. Please have a look
at the ``doc/example.txt`` that explains how the user environment is setup
with Modules at the University of Minnesota computer science department.


Build and installation options
------------------------------

Options available at the ``./configure`` installation step are described
below.  These options enable to choose the installation paths and the
features to enable or disable. You can also get a description of these
options by typing ``./configure --help``.

Fine tuning of the installation directories (the default value for each option
is displayed within brakets):

--prefix=PREFIX       Installation root directory [``/usr/local/Modules``]
--bindir=DIR          Directory for executables reachable by users
                      [``PREFIX/bin``]
--libexecdir=DIR      Directory for executables called by other executables
                      like modulecmd.tcl [``PREFIX/libexec``]
--etcdir=DIR          Directory for the executable configuration scripts
                      [``PREFIX/etc``]
--initdir=DIR         Directory for the per-shell environment initialization
                      scripts [``PREFIX/init``]
--datarootdir=DIR     Base directory to set the man and doc directories
                      [``PREFIX/share``]
--mandir=DIR          Directory to host man pages [``DATAROOTDIR/man``]
--docdir=DIR          Directory to host documentation other than man
                      pages like README, license file, etc
                      [``DATAROOTDIR/doc``]
--modulefilesdir=DIR  Directory or main modulefiles also called system
                      modulefiles [``PREFIX/modulefiles``]

Optional Features (the default for each option is displayed within
parenthesis, to disable an option replace ``enable`` by ``disable`` for
instance ``--disable-set-manpath``):

--enable-set-manpath  Prepend man page directory defined by the ``--mandir``
                      option to the MANPATH environment variable in the shell
                      initialization scripts. (default=yes)
--enable-set-binpath  Prepend binary directory defined by the ``--bindir``
                      option to the PATH environment variable in the shell
                      initialization scripts. (default=yes)
--enable-dotmodulespath
                      Set the module paths defined by ``--with-modulepath``
                      option in a ``.modulespath`` file (following C version
                      fashion) within the initialization directory defined by
                      the ``--initdir`` option rather than within the
                      ``modulerc`` file. (default=no)
--enable-doc-install  Install the documentation files in the documentation
                      directory defined with the ``--docdir`` option. This
                      feature has no impact on manual pages installation.
                      Disabling documentation file installation is useful in
                      case of installation process handled via a package
                      manager which handles by itself the installation of
                      this kind of documents. (default=yes)
--enable-example-modulefiles
                      Install some modulefiles provided as example in the
                      system modulefiles directory defined with the
                      ``modulefilesdir`` option. (default=yes)
--enable-compat-version
                      Build and install the Modules compatibility (C) version
                      in addition to the main released version. This feature
                      also enables switching capabilities from initialization
                      script between the two installed version of Modules (by
                      setting-up the ``switchml`` shell function or alias).
                      (default=yes)
--enable-versioning   Append Modules version to installation prefix and deploy
                      a ``versions`` modulepath shared between all versioning
                      enabled Modules installation. A modulefile corresponding
                      to Modules version is added to the shared modulepath and
                      enables to switch from one Modules version to another.
                      (default=no)

Optional Packages (the default for each option is displayed within
parenthesis, to disable an option replace ``with`` by ``without`` for
instance ``--without-modulepath``):

--with-tclsh=BIN      Name or full path of Tcl interpreter shell
                      (default=\ ``tclsh``)
--with-pager=BIN      Name or full path of default pager program to use to
                      paginate informational message output (can be superseded
                      at run-time by environment variable)
                      (default=\ ``less``)
--with-pager-opts=OPTLIST
                      Settings to apply to default pager program
                      (default=\ ``-eFKRX``)
--with-modulepath=PATHLIST
                      Default path list to setup as the default modulepaths.
                      Each path in this list should be separated by ``:``.
                      Defined value is registered in the ``modulerc`` or
                      ``.modulespath`` configuration file, depending on the
                      ``--enable-dotmodulespath`` option. This value is read
                      at initialization time to populate the MODULEPATH
                      environment variable. By default, this modulepath is
                      composed of the directory set for the system modulefiles
                      (default=\ ``PREFIX/modulefiles`` or
                      ``BASEPREFIX/$MODULE_VERSION/modulefiles`` if versioning
                      installation mode enabled)
--with-loadedmodules=MODLIST
                      Default modulefiles to load at Modules initialization
                      time. Each modulefile in this list should be separated
                      by ``:``. Defined value is registered in the
                      ``modulerc`` configuration file. (default=no)
--with-quarantine-vars=<VARNAME[=VALUE] ...>
                      Environment variables to put in quarantine when running
                      the module command to ensure it a sane execution
                      environment (each variable should be separated by space
                      character). A value can eventually be set to a
                      quarantine variable instead of emptying it. (default=no)
