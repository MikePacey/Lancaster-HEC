The GNU Scientific Library (GSL)
================================

The HEC provides the GNU Scientific Library (GSL), 
a numerical library for C and C++. The library offers 
a wide variety of numerical and statistical routines, 
as described on the GSL Home Page (see Helpful links 
under Additional advice).

To view the different module versions of GSL available, 
run the following command:

.. code-block:: console

  module avail gsl

Module names decribe the version of GSL offered, along 
with the supported compiler.

The default GSL module has been compiled with the Intel compiler 
suite and includes the optimised BLAS provided by the Intel 
Math Kernel Library. To access both the library and the 
Intel compilers, simply add the gsl module to your login environment:

.. code-block:: console

  module add gsl

or to request a specific version, use the full module name which 
will be suffixed with *-intel*. For example:

.. code-block:: console

  module add gsl/1.16-intel

Using GSL with Intel compilers
------------------------------

To compile a stand-alone C source code from file myprogram.c:

.. code-block:: console

  icc -I$GSLINC -o my_program my_program.c -L$GSLLIB -lgsl -limf

The resulting executable will be named *my_program*.

The gsl module creates two additional environment variables to allow 
for ease of compiling: *$GSLINC* points to GSL's header file directory, 
while *$GSLLIB* points to the location of all the GSL libraries. For 
more complex builds, these can be added to Makefiles, configuration script 
commands, etc.

A sample serial batch job script for an application built with Intel GSL

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial

  source /etc/profile
  module add gsl/1.16-intel

  ./my_program

Using GSL with GNU compilers
----------------------------

Versions of gsl compiled for the GNU Compiler Collection 
have the suffix *-gcc* in their module name. It can be loaded 
with the relevant module command, e.g.:

.. code-block:: console

  module add gsl/1.16-gcc

To compile a stand-alone C source code from file myprogram.c:

.. code-block:: console

  gcc -I$GSLINC -o my_program my_program.c -L$GSLLIB -lgsl -lgslcblas -lm

The resulting executable will be named my_program.

An example serial batch job for an application built with GNU GSL
-----------------------------------------------------------------

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial

  source /etc/profile
  module add gsl/2.7-gcc

  ./my_program

Further Reading
---------------

Further information can be found one the following pages:

* The GSL `Home Page <https://www.gnu.org/software/gsl/>`_

* The GSL `Library Manual <https://www.gnu.org/software/gsl/doc/html/>`_
