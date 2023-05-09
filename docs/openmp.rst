OpenMP parallel jobs
====================

OpenMP is a set of compiler directives, library routines and 
environment variables that can be used to specify shared-memory 
parallelism in C, C++ and Fortran codes.

OpenMP compiler directives can be inserted into source code to indicate 
to the compiler which sections of code can be readily parallelised, 
allowing a programmer to highlight the core sections of code that can 
benefit from OpenMP's automated parallelisation. When compiled with a 
special compiler flag, the compiler will create a multi-threaded version 
of the application that can automatically distribute the annotated 
sections of code across multiple CPUs on the same compute node.

It should be stressed that not all codes will benefit from such attempts 
at parallelism, and not all sections of parallel code scale well to 
high CPU/thread counts. Users should test serial and parallel versions 
of their code to ensure that the parallel version is making good use of 
the additional processors.

Programming and Compiling with OpenMP
-------------------------------------

A detailed explanation of the OpenMP compiler directives can found in the 
guides for both the PGI and Intel compiler suites (see helpful links under 
the Additional advice section for more information).

* To compile OpenMP code using the Intel compiler, compile with your normal set of Intel compiler flags, and add the **-openmp** argument.

* To compile OpenMP code using the PGI compiler, compile with your normal set of PGI compiler flags, and add the compiler argument **-mp**

* To compile OpenMP code using the GNU compilers, compile with your normal set of GNU compiler flags and add the compiler argment **-fopenmp**

Make sure that the correct module for your preferred compiler suite has already been added to your environment.

Submitting OpenMP jobs
----------------------

The following job template will run an 8-core version of a program names
**omptest** compiled with an Intel compiler:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH --cpus-per-task=8
  #SBATCH --mem=4000M

  source /etc/profile
  module add intel/21.0u4

  ./omptest
  
The number of cores/threads required is specified in the 
--cpus-per-task option in the job script. 

Note that a *memory 
resource request* is required for OpenMP jobs - as the job is 
unlikely to use all cores on a single compute node, the job 
scheduler needs to know what memory to reserve for this job to 
enable it to schedule other jobs with their own memory resource 
requests on the same node. If no memory resource request is 
specified, the default value of 500M is applied ot the job.

OpenMP applications automatically set their number of threads 
based upon the value of the environment variable *OMP_NUM_THREADS*. 
For OpenMP jobs using the template above, the scheduler will 
automatically set this environment variable to match the CPUs requested 
in the --cpus-per-task option in the job script.
