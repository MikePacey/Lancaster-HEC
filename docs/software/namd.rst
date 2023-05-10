The NAMD molecular dynamics simulator
=====================================

NAMD is a parallel molecular dynamics code designed 
for high-performance simulation of large biomolecular 
systems. The current version available on the HEC 
is the multi-threaded version, which can run in parallel 
using cores on the same compute node, similar to the 
OpenMP model of parallelism (read more about OpenMP 
on the Using OpenMP on the HEC page).

To access the default version of NAMD on the HEC, simply 
type the command:

.. code-block:: console

  module add NAMD

To see what other versions of NAMD are currently available, type:

.. code-block:: console

  module avail NAMD

See the Using environment modules on the HEC page for more 
details on the module command.

A sample parallel job script for NAMD
-------------------------------------

NAMD may be run by creating a parallel job script 
(for example, named NAMD_job.com) similar to the following, 
which will submit the model in input file Efield_1.inp 
to run on 16 cores:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH --cpus-per-task=16
  #SBATCH --mem=1G

  source /etc/profile
  module add NAMD/2.12-mp

  namd2 +p${OMP_NUM_THREADS} Efield_1.inp > Efield_1.log

The script is then submitted for running by typing:

.. code-block:: console

  sbatch NAMD_job.com

Further Reading
---------------

The NAMD `Home Page <http://www.ks.uiuc.edu/Research/namd/>`_
