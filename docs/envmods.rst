Using environment modules
=========================

.. _envmods:

About environment modules
-------------------------

The environment modules are a system that allows you to quickly 
and easily modify your shell environment to access different software 
packages. The HEC offers a large (and growing) number of software 
packages to users, and each package may contain several tools, 
manual pages and libraries, or may require special setup to work 
properly. Some software packages come in several versions or 
flavours, many of which conflict with each other. Modules allow 
you to tailor your shell to access exactly the versions of 
packages you need by setting up the relevant environment variables 
for you, and automatically avoiding many possible conflicts between packages.

Viewing available modules
-------------------------

To view the available modules, enter the following:

.. code-block:: console

  module avail

A typical output would look like this:

.. code-block:: console

  --------- /usr/shared_apps/Modules/compilers ---------
  cuda/5.0   openmpi/1.6.5-gcc   pgi/13.5
  intel/13.0 openmpi/1.6.5-intel

  --------- /usr/shared_apps/Modules/libraries ---------
  fftw/3.3.3 gsl/1.16

  ----------- /usr/shared_apps/Modules/apps -----------
  R/3.0.1 matlab/2013a stata/12 valgrind/3.8.1

For each available module, the name of the software package is listed first, 
followed by an optional forward slash (/) and the version or flavour of the 
package offered. Where multiple versions or flavours of a package exist, 
the default version is indicated. The default version can be accessed 
using just the module name, rather than the full name.

A brief description of each software package can be obtained with the 
**module whatis** command:

.. code-block:: console

  wayland> module whatis matlab
  matlab : The MATLAB numerical computing environment (2010b)

  homepage: http://www.mathworks.com/

As the full list of modules is lengthy, you can type **module avail packagename** 
to list versions of the specified package name. For example:

.. code-block:: console

  wayland> module avail intel

  --------- /usr/shared_apps/Modules/modulefiles ---------
  intel/10.1 intel/11.1 intel/12.1(default)

Using modules
-------------

To setup up your job or login shell environment to access the required package, 
use the module add command. For example:

.. code-block:: console

  module add intel

The above command will configure your environment to use the current default 
version of the Intel Compiler Suite tools, which in the example output from 
**module avail** above would version 12.1. To request a specific version of a 
package (e.g. version 11.1), use the full module name:

.. code-block:: console

  module add intel/11.1

To view the modules currently added to your environment, use the **module list** command:

.. code-block:: console

  wayland> module list
  Currently Loaded Modulefiles:
  1) intel/11.1

To remove modules from your environment, use the **module rm** command:

.. code-block:: console
  wayland> module rm intel

  wayland> module list
  No Modulefiles Currently Loaded.

Module conflicts
----------------

There are certain module combinations which may cause problems when 
both are present in your environment. Examples include cases where 
modules offer applications or libraries with identical names (which 
may cause confusion as to exactly which one is being used), or 
when an application is strongly dependent on a specific compiler 
and its libraries (in which case the module will itself 
set up access to the required compiler version)

In such cases, modules are designed to conflict. Attempting to load 
a module which conflicts with one already added to your environment 
will generate a warning message and the conflicting module will not be 
loaded. For example, compiler suites are set to conflict with each 
other so that you only ever have a single compiler and its associated 
libraries available in your environment at any one time:

.. code-block:: console

  wayland> module add intel
  wayland> module add pgi
  pgi/11.5(15):ERROR:150: Module 'pgi/11.5' conflicts with the currently loaded module(s) 'intel/12.1'
  pgi/11.5(15):ERROR:102: Tcl command execution failed: conflict pgi intel

The first error line shows exactly which two modules generate the conflict. 
The second error message shows the general rule that triggered the warning 
message.

To resolve a conflict simply remove the currently loaded conflicting module 
before adding the new one.

Customising your environment
----------------------------

Module instructions do not persist across sessions; if you log out and log 
back in again, no modules will be loaded in your new login session. If 
there are a core set of packages which you access regularly, you can place 
relevant module add commands into your shell startup script - *$HOME/.profile* 
for bash.

Switching to different versions of modules
------------------------------------------

Sometimes you may wish to switch between different versions of a software 
package. Rather than invoke module rm followed by module add, you can 
simply use module switch instead:

.. code-block:: console

  % module add pgi/6.2-64-bit

  % module list
  Currently Loaded Modulefiles:
  1) pgi/6.2-64-bit

  % module switch pgi/6.2-32-bit

  % module list
  Currently Loaded Modulefiles:
  1) pgi/6.2-32-bit

Viewing module contents
-----------------------

If you want to see what environment setup a module is doing behind the scenes, 
use the command **module show**. For example:

.. code-block:: console

  > module show R/2.11.1
  -----------------------------------------------
  /usr/shared_apps/Modules/modulefiles/R/2.11.1:

  module-whatis the R stats package version 2.11.1

  homepage: http://cran.r-project.org/
  conflict R
  prepend-path PATH /usr/shared_apps/packages/R-2.11.1/bin
  prepend-path LD_LIBRARY_PATH /usr/shared_apps/packages/R-2.11.1/lib64/R/lib

