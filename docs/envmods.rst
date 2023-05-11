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

To view the available modules, use the command ``module avail``

A typical output would look like this:

.. code-block:: console

  ------------------------------ /usr/shared_apps/Nmodules/apps ------------------------------
  abaqus/2019             dynare/4.4.3              mercurial/3.2          turbomole/6.6      
  adf/test                e4d/Mar2017-dev           mpb/1.5-mpi            turbomole/6.6-smp  
  amber/18                emacs/27.1                mpb/test-ser           turbomole/7.3-mpi 

  --------------------------- /usr/shared_apps/Nmodules/compilers ----------------------------
  gcc/4.8.1   intel/16.0     java/13.0.1             openmpi/1.10.1-intel  pgi/12.5  
  gcc/4.9.2   intel/16.0u3   mono/4.2.2              openmpi/1.10.4-gcc    pgi/13.5 

  --------------------------- /usr/shared_apps/Nmodules/libraries ----------------------------
  armadillo/test          fftw/3.3.6        hdf5/1.8.13-intel       petsc/3.6.3              
  boost/1.54.0-gcc        fftw/3.3.8        hdf5/1.10.5-ompi-intel  petsc/3.12.5  


For each available module, the name of the software package is listed first, 
followed by an optional forward slash (/) and the version or flavour of the 
package offered. Where multiple versions or flavours of a package exist, 
the default version is indicated. The default version can be accessed 
using just the module name, rather than the full name.

As the full list of modules is lengthy, you can type ``module avail packagename``
to list versions of the specified package name. For example:

.. code-block:: console

  --------------------------- /usr/shared_apps/Nmodules/compilers ----------------------------
  intel/12.1  intel/15.0  intel/16.0u3  intel/18.0u5  intel/20.0u3  
  intel/13.0  intel/16.0  intel/17.0u4  intel/19.0u5  intel/21.0u4  

  Key:
  default-version  modulepath 

A brief description of each software package can be obtained with the 
``module whatis`` command:

.. code-block:: console

  wayland-2022% module whatis intel/21.0u4 

  --------------------------- /usr/shared_apps/Nmodules/compilers ----------------------------
          intel/21.0u4: the intel oneapi compiler suite, 2021 version 4

  homepage: https://www.intel.com/content/www/us/en/developer/tools/oneapi/toolkits.html#base-
kit

Using modules
-------------

To setup up your job script or login shell environment to access the required package, 
use the ``module add`` command. For example:

.. code-block:: console

  module add intel

The above command will configure your environment to use the current default 
version of the Intel Compiler Suite tools, which in the example output from 
``module avail`` above would version 12.1. To request a specific version of a 
package (e.g. version 11.1), use the full module name:

.. code-block:: console

  module add intel/21.0u4

To view the modules currently added to your environment, use the **module list** command:

.. code-block:: console

  wayland-2022% module list
  Currently Loaded Modulefiles:
   1) intel/21.0u4 

To remove modules from your environment, use the **module rm** command:

.. code-block:: console

  wayland-2022% module rm intel
  wayland-2022% module list
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

  wayland-2022% module add intel
  wayland-2022% module add pgi
  Loading pgi/19.4
    ERROR: Module cannot be loaded due to a conflict.
      HINT: Might try "module unload intel/20.0u3" first.

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

  wayland-2022% module add intel/21.0u4

  wayland-2022% module list
  Currently Loaded Modulefiles:
   1) intel/21.0u4  

  wayland-2022% module switch intel/19.0u5

  wayland-2022% module list
  Currently Loaded Modulefiles:
   1) intel/19.0u5 

Viewing module contents
-----------------------

If you want to see what environment setup a module is doing behind the scenes, 
use the command ``module show``. For example:

.. code-block:: console

  wayland-2022% module show ImageMagick/7.0.9
  -------------------------------------------------------------------
  /usr/shared_apps/Nmodules/apps/ImageMagick/7.0.9:

  module-whatis   {The ImageMagick image editting package

  homepage: https://imagemagick.org//}
  setenv          IM_HOME /usr/shared_apps/packages/ImageMagick-7.0.9-14
  prepend-path    PATH /usr/shared_apps/packages/ImageMagick-7.0.9-14/bin
  prepend-path    MANPATH /usr/shared_apps/packages/ImageMagick-7.0.9-14/share/man
  prepend-path    LD_LIRBARY_PATH /usr/shared_apps/packages/ImageMagick-7.0.9-14/lib
  -------------------------------------------------------------------

