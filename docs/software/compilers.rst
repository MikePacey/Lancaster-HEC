Using C and Fortran compilers on the HEC
========================================

The following compilers are available on the HEC:

* Intel C/C++;
* Intel Fortran 77, 90 & 95;
* PGI (Portland) C/C++; and
* PGI (Portland) Fortran 77, 90 and 95.

Intel Compilers
---------------

To access the Intel Compiler suite, load the intel module:

.. code-block:: console

  module add intel

The Intel Compilers (**icc** for C, **icpc** for C++ and **ifort** 
for Fortran) can now be invoked. A detailed list of the flags for 
these compilers can be found in the relevant manual pages.

The recommended starting point for generating optimised code is to use the flags: 

.. code-block:: console

  -O3 -xCORE-AVX-I -axCORE-AVX2,CORE-AVX512

This will generate code optimised for all the CPU architectures currently available on the HEC.

To improve numerical precision add the flag: 
  
.. code-block:: console

  -fp-model precise

Intel support for BLAS, LAPACK, etc - the Math Kernel Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The current version of the Intel compiler suite features a built-in 
version of Intel's Math Kernel Library, offering several popular 
linear algebra (BLAS and LAPACK), FFT and PDE functions. The 
recommended compiler flag for linking with the MKL is: 

.. code-block:: console  

  -mkl=sequential

PGI Compilers
-------------

Note: NVidia have discontinued support for the old licensed 
versions of the PGI compiler, so any new software builds 
should use the NVidia HPC SDK described below. The old pgi 
modules are retained only to allow backward compatibility with 
older software built with them.

To access the Portland Group compiler suite, load the nv-hpc-sdk module:

.. code-block:: console  

  module add nv-hpc-vsdk

The Portland Compilers (**pgcc**, **pgCC**, **pgf77**, **pgf90** and **pgf95**) 
can now be invoked. The recommended starting point for optimising code is to add the flag:

.. code-block:: console

  -fastsse 

PGI support for BLAS, LAPACK, etc
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Recent versions of the PGI suite are installed with optimised versions of BLAS and LAPACK based on OpenBLAS. These can be linked via the commands: 

.. code-block:: console

  -lblas

and

.. code-block:: console

  -llapack

Version 14 and earlier of the PGI suite offered BLAS, etc as 
part of ACML - the AMD Core Math Library - which can be linked 
using the flags: 

.. code-block:: console

  -Mcache_align_ -lacml
