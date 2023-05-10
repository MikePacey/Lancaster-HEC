The LAMMPS molecular dynamics simulator
=======================================

LAMMPS is a classical molecular dynamics code, and an 
acronym for Large-scale Atomic/Molecular Massively Parallel Simulator.

LAMMPS has potentials for solid-state materials (metals, semiconductors) 
and soft matter (biomolecules, polymers) and coarse-grained or mesoscopic 
systems. It can be used to model atoms or, more generically, as a 
parallel particle simulator at the atomic, meso, or continuum scale.

To access the default version of LAMMPS on the HEC, simply type the command:

.. code-block:: console

  module add lammps

To see what other versions of LAMMPS are currently available, type:

.. code-block:: console

  module avail lammps

For more details on the module command, see Using environment modules on the HEC.

A sample parallel batch job script for LAMMPS
---------------------------------------------

LAMMPS batch jobs may be run by creating a batch job script 
(for example, called LAMMPS_job.com) like the following, which 
will run on two 40-core compute nodes using an input file 
called in.wall.2d:

.. code-block:: bash

  #$ -S /bin/bash

  #$ -q parallel
  #$ -l nodes=2
  #$ -l node_type=40core192G

  source /etc/profile
  module add lammps

  cat $PE_HOSTFILE

  mpirun lmp-mpi -in in.wall.2d

The script is then submitted for running by typing:

.. code-block:: console

  sbatch LAMMPS_job.com

Further Reading
---------------

Further information can be found on the following pages:

* The LAMMPS `Home Page <https://www.lammps.org/>`_

* The LAMMPS `Online Manual <https://docs.lammps.org/Manual.html>`_
