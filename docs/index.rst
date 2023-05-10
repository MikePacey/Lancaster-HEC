HEC 3.0 Documentation
=====================

This documentation is a guide to using the HEC 3.0 test bed,
in particular the SLURM jobs scheduler which will replace
the current SGE job scheduler. The structure of this guide
is broadly similar to the current official HEC user guide
so you can easily compare the two.

.. note:: 
   The HEC 3.0 test bed is under development, so check
   back regularly to see what changes have been made to best practice.

Contents
--------

.. toctree::
   :caption: Overview of the HEC
   :maxdepth: 1

   intro 
   howitworks

HEC basics
.. toctree::
   :maxdepth: 1

   login
   linux
   filestore
   envmods

Job submission and monitoring
.. toctree::
   :maxdepth: 1

   submit
   monitoring
   largemem
   jobarrays

Parallel job submission
.. toctree::
   :maxdepth: 1

   parallel
   mpi
   openmp
   nodetypes

Job templates for popular software
.. toctree::
   :maxdepth: 1

   soft
   software/compilers
   software/ansys
   software/comsol
   software/cp2k
   software/cst
   software/gromacs
   software/gsl
   software/lammps
   software/namd
   software/r.rst
   software/stata.rst

