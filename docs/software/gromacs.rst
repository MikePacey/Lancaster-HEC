GROMACS
=======

GROningen MAchine for Chemical Simulations (GROMACS) is a 
molecular dynamics package mainly designed for simulations 
of proteins, lipids and nucleic acids.

Invoking GROMACS on the HEC
---------------------------

To access the default version of GROMACS on the HEC, simply type the command:

.. code-block:: console

  module add gromacs 

To see what other versions of GROMACS are currently available, type:

.. code-block:: console

  module avail gromacs

For more details on the module command, see Using environment modules on the HEC.

Some versions of GROMACS have been built to support double precision arithmetic, 
so all tools must be called with a *_d* suffix. To call the parallel (MPI) 
double-precision versions of GROMACS tools, add the suffix *_mpi_d*.

A sample parallel batch job for GROMACS
---------------------------------------

GROMACS batch jobs may be run by creating a batch job script.
The following example runs a single-precision build of GROMACS 
on two 16-core compute nodes:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH -C node_type=10GethAny
  #SBATCH --nodes=2
  #SBATCH --cpus-per-task=8
  #SBATCH --exclusive

  source /etc/profile
  module add gromacs/2020

  # Pre=processing (serial)
  gmx grompp -f md.mdp -c npt.gro -p topol.top -o md_0_1.tpr 

  # Main application - parallel
  mpirun --bind-to none gmx_mpi mdrun -deffnm md_0_1

The script is then submitted for running by typing:

.. code-block:: console

  sbatch GROMACS_job.com

The job template is a little different from the general MPI batch job 
template, as it's recommended to run GROMACS using hybrid parallelism - 
both MPI and OpenMP. The recommended configuration is for the number of 
MPI processes per node to equal the number of sockets and the number of 
threads per MPI process to match the number of cores per socket. As all 
HEC compute nodes are currently two-socket system the 16-core *10GethAny* 
node_type setup is therefore 2 MPI processes per node with 8 threads per 
process. This is achieved with the additional resource request 
``--cpus-per-task=8`` which sets OMP_NUM_THREADS to 8 and creates an mpirun 
hostfile with (16 / 8) = 2 processes per node.

Further Reading
---------------

Further information can be found in the GROMACS `User Manuals <https://manual.gromacs.org/documentation/>`_
