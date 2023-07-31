Message Passing Interface (MPI) parallel jobs
=============================================

Multi-node (distributed memory) parallel jobs are typically 
written using MPI, a C/C++ and Fortran based Application Programming 
Interface that implements job parallelism via passing (network) messages 
between co-ordinated processes.

Compiling MPI codes
-------------------

The HEC currently supports the OpenMPI implementation of MPI, available 
through one of several modules described below. The implementation offers 
compiler wrappers to assist with compiling MPI codes. The wrappers call 
an underlying compiler, and automatically take care of locating the correct 
version of MPI libraries and include files. The compiler wrappers are:

.. list-table::
  :header-rows: 1

  * - Wrapper name
    - Supported Language
  * - mpicc
    - C
  * - mpiCC
    - C++
  * - mpif77
    - Fortran 77
  * - mpif90
    - Fortran 90

These wrappers can be called once the relevant :ref:`environment module<envmods>` has been 
added to your environment and are used in exactly the same way 
that a standard compiler can be called.

More complex MPI applications are typically built with a Makefile 
and/or a configure script. These applications can be built by 
specifying the compiler wrappers in place of the general compilers. 
It isn't necessary to specify the location or name of the MPI libraries 
or header files - the wrappers will handle these details automatically - 
so these fields can typically be left blank in a Makefile.

Each version of OpenMPI comes in one of three flavours, one for each 
of the three supported compilers on the HEC: gcc, intel and pgi. For 
example, the version of OpenMPI 4.1.3 built for Intel compilers will 
have the module name: openmpi/4.1.3-intel. When submitting MPI jobs, 
care should be take to load the same module version and flavour as 
was used to originally build it. Mix and matching compiler versions 
can often cause library conflicts and other errors.

Submitting an MPI job
---------------------

Here is a sample job to run a parallel application a called **myapp**
across 2 compute nodes, with 16 cores per node:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH --exclusive
  #SBATCH --nodes=2
  #SBATCH -C node_type=10Geth64G

  source /etc/profile
  module add openmpi/4.1.3-intel

  mpirun myapp

Anatomy of an MPI job
~~~~~~~~~~~~~~~~~~~~~

**Queue selection**

.. code-block:: bash

  #SBATCH -p parallel

This line signals to the job scheduler that this should be run on the parallel partition
(queue), which offers full support for parallel jobs.

**Specifying job size (number of nodes and cores used)**

The next three lines together specify the size of parallel job you're requesting.

.. code-block:: bash

  #SBATCH --exclusive

This line indicates that the job is requesting exclusive access to one or 
more compute nodes. The requested node(s) will be used exclusively for the 
parallel job - no other serial or parallel jobs may co-exist on the selected 
compute nodes.

.. code-block:: bash

  #SBATCH --nodes=2

This line indicates that the job requires two whole compute nodes.

.. code-block:: bash

  #SBATCH -C node_type=10Geth64G

This line specifies which *type* of compute node the job is requesting. 
Different nodes have different architectures, numbers of cores and amounts 
of memory; to ensure optimum placement all nodes used in the same parallel 
job must be of the same type. The different types of node are described 
in full in the section :ref:`Requesting specific node types<envmods>`

In this example, the node type is *10Geth64G*, which has 16 cores, 64G of 
memory and 10G ethernet network connection.

**Environment setup**

.. code-block:: bash

  source /etc/profile
  module add openmpi/4.1.3-intel

These two lines set up the job's shell environment and then selects a specific OpenMPI module.

**Calling the application**

.. code-block:: bash

  mpirun myapp

This line is the call to the parallel application (in this case **myapp**) 
wrapped in a call to the **mpirun** application which will handle the 
parallel startup of the user application. Note that mpirun does not need 
to be told the number of processes or nodes to run on; OpenMPI automatically 
picks up this information from the job environment based upon the resource 
requests in the job script.

The name of the MPI application should typically be the last argument to mpirun. 
For MPI applications that require their own additional arguments, you should place 
them after the call to the application itself, as arguments before the application 
call are interpreted by the mpirun command.

Testing suggests OpenMPI supports basic input redirection on the assumption that 
standard input is read by rank zero of the application.

A note on memory resource requests for MPI jobs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As exclusive parallel jobs reserve whole nodes, memory reservation is 
set automatically based on the selected compute node type's full memory - 
any memory resource requests made in the job script or the sbatch command 
line will be over-ridden. Note: as this feature takes memory resource 
requests outside of the user's control, memory efficiency values in reporting 
tools like seff can be ignored.
