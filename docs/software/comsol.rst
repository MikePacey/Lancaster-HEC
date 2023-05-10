The COMSOL Multiphysics Package
===============================

The HEC currently hosts versions of the COMSOL multiphysics 
modelling suite, with licenses supplied by the Engineering 
(for version 5.2a) and Physics (for version 5.1) departments. 
Please check with the relevant Department's IT Liaison to 
ensure you have access to these externally-hosted licenses.

A sample serial job script for COMSOL
-------------------------------------

COMSOL batch serial jobs may be run by creating a batch job control 
script (for example, called comsol_job.com) like the following:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial
  #SBATCH --mem=2G

  source /etc/profile
  module add comsol/5.1

  comsol batch -prefsdir $TMPDIR  -recoverydir $TMPDIR \
    -inputfile cylinder_flow.mph -outputfile cylinder_flow.out \
    -autosave off

For an introduction to serial batch jobs on the HEC, please refer 
to Submitting jobs on the HEC.

The above job takes the model described in cylinder_flow.mph and 
places the results in cylinder_flow.out.

The *-prefsdir* and *-tmpdir* values are set to point to a temporary 
directory created during the job run. This is recommended as by 
default COMSOL writes a large number of files to the user home area, 
which can quickly result in going over quota.

The *-recoverydir* option sets the recovery directory to the 
current working directory from which the job was submitted.

A sample parallel job script for COMSOL
---------------------------------------

For models suitable for parallel running, the following parallel 
job template can be used:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH -C node_type=10GethAny
  #SBATCH --nodes=2
  #SBATCH --ntasks-per-node=2
  #SBATCH --exclusive

  source /etc/profile
  module add comsol/5.1

  comsol batch -mpibootstrap slurm -np $OMP_NUM_THREADS \
    -prefsdir $TMPDIR -recoverydir `pwd`  -tmpdir $TMPDIR \
    -inputfile cylinder_flow.mph -outputfile cylinder_flow.out \
    -autosave off

For an introduction to parallel MPI jobs on the HEC, please see 
Using the Message Passing Interface (MPI) on the HEC.

It is recommended to run COMSOL in hybrid parallel mode, with 
one MPI process per CPU socket and the number of threads per 
process matching the number of CPU cores per socket. The above 
example requests two compute nodes of node_type 10GethAny - these 
nodes each have two sockets, and 8 cores per socket.

The job script contains an additional SLURM resource request line 
advising that the job requires two tasks per node - these will be 
the two MPI processes per compute node. The additional *-np* argument 
to comsol uses the environment variable OMP_NUM_THREADS which will be 
automatically set up by the job sceduler to a value based on the 
formula **(# cpus per node / # of tasks per node)**. For the 
10GethAny nodes with 16 CPUs, this will result in an OMP_NUM_THREAD 
value of 8 - so comsol will run with two MPI proceses per compute node, 
and 8 threads per MPI process.

Two important items to bear in mind when running COMSOL in parallel, 
to ensure that resources are not wasted:

* Significant speedup is only seen on models with several million degrees 
  of freedom; and
* The meshing algorithm runs mostly in serial. For parallel runs, the mesh 
  should be pre-computed in serial mode

Further Reading
---------------

The COMSOL `home page <https://www.comsol.com/>`_
