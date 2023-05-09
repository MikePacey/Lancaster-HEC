ANSYS
-----

The HEC currently hosts several components of the commercial Ansys package, 
with licenses supplied by the Engineering Department.

A sample serial job script for cfx5solve
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Cfx5solve batch jobs may be run by creating a batch job control script i
(such as the example below, which uses ansys_job.com):

.. highlight:: bash
#!/bin/bash

#SBATCH -p serial
#SBATCH --mem=4G

source /etc/profile
module add ansys/21.2

echo $MYHOSTLIST

cfx5solve -batch -def mymodel.def
::

A sample parallel job script for cfx5solve for Ansys 19 or earlier
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For models suitable for parallel running, the following parallel job i
template can be used, which runs on 2 of the 16-core compute nodes:

.. highlight:: bash
#!/bin/bash

#SBATCH -p parallel
#SBATCH --nodes=2
#SBATCH -C node_type=10GethAny
#SBATCH --exclusive

source /etc/profile
module add ansys/17.2

echo $MYHOSTLIST

cfx5solve -batch -def mymodel.def \
  -par-dist "$MYHOSTLIST" -start-method "HP MPI Distributed Parallel" 
::

A sample parallel job script for cfx5solve for Ansys 20 or later
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For models suitable for parallel running, the following parallel job 
template can be used, which runs on two of the 16-core compute nodes:

.. highlight:: bash
#!/bin/bash

#SBATCH -p parallel
#SBATCH --nodes=2
#SBATCH -C node_type=10GethAny
#SBATCH --exclusive

source /etc/profile
module add ansys/21.2

echo $MYHOSTLIST

cfx5solve -batch -def mymodel.def -par-dist "$MYHOSTLIST" \
    -start-method "Open MPI Distributed Parallel"
::

where mymodel.def is the name of the Solver Input File. Ansys uses 
MPI for parallelism - see Using the Message Passing Interface (MPI) 
on the HEC for more details on MPI job scripts.

A sample parallel job script for fluent for Ansys 19 or earlier
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For models suitable for parallel running, the following parallel 
job template can be used:

.. highlight:: bash
#!/bin/bash

#SBATCH -p parallel
#SBATCH --nodes=2
#SBATCH --exclusive
#SBATCH -C node_type=10Geth64G

source /etc/profile
module add ansys/19.1

fluent 3ddp -g -slurm -t${NSLOTS} -pdefault -i runjob.jou
::

Where runjob.jou is the name of your fluent journal file. Ansys
uses MPI for parallelism - see Using the Message Passing Interface 
(MPI) on the HEC for more details on MPI job scripts.

A sample parallel job script for fluent for Ansys 20 and later
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For models suitable for parallel running, the following parallel 
job template can be used:

.. highlight:: bash
#!/bin/bash

#SBATCH -p parallel
#SBATCH --nodes=2
#SBATCH --exclusive
#SBATCH -C node_type=10Geth64G

source /etc/profile
module add ansys/19.1

fluent 3ddp -g -slurm -t${NSLOTS} -mpi=openmpi -i runjob.jou
::

Where runjob.jou is the name of your fluent journal file. Ansys 
uses MPI for parallelism - see Using the Message Passing Interface 
(MPI) on the HEC for more details on MPI job scripts.

Further Reading
~~~~~~~~~~~~~~~

Further information be found on `The Official Ansys Home Page <http://www.ansys.com/>`_
