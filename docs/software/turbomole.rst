Turbomole
=========

The HEC hosts serial and SMP parallel versions of the Turbomole ab initio 
Electronic Structure Calculations package. The package is licensed to 
Andrew Kerridge in the Chemistry Department. Please contact him to 
authorise access to the software.  Further information can be found 
on the Turbomole website.

Further information can be found on the `Turbomole website<http://www.turbomole.com/>`

Viewing available verisons of turobmole
---------------------------------------

To view the currently available turbomole modules, run the command
``module av turbomole``. It will produce output similar to this:

.. code-block:: bash

  --------- /usr/shared_apps/Nmodules/apps ----------
  turbomole/6.6  turbomole/6.6-smp  turbomole/7.3-mpi 
  
The default is the serial version.

Serial turbomole batch jobs may be run by creating a batch job 
script (for instance, called *jobex.com*) like the following:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH -C node_type=10GethAny
  #SBATCH --nodes=1
  #SBATCH --exclusive
 
  source /etc/profile
  module add turbomole/6.6

  jobex

The final line in the script is a call to the relevant turbomole script 
(in this case jobex). Note that no additional environment variables 
need to be set up - this is all handled by the turbomole module file. 
The script is then submitted for running by running the following 
from the directory containing your job's data files: ``qsub jobex.com``

The job submission template above follows the same template as a general 
serial batch job. You can read more about this on 
the :ref:`Submitting jobs on the HEC<job submit>`_ page under the Batch jobs section.

A sample batch job script for SMP parallel jobex
------------------------------------------------

For job types which support SMP parallel versions of Turbomole 
applications, the following template job script can be used:


.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH --cpus-per-task=8
  #SBATCH --mem=10G
 
  source /etc/profile
  module add turbomole/6.6-smp

  export TM_PAR_FORK=ON

  jobex

A sample batch job script for serial NumForce
---------------------------------------------

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial
  #SBATCH --mem=10G

  source /etc/profile
  module add turbomole/6.6

  NumForce

A sample batch job script for SMP parallel NumForce
---------------------------------------------------

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH --cpus-per-task=8
  #SBATCH --mem=10G
 
  source /etc/profile
  module add turbomole/6.6-smp

  NumForce -mfile $TMPDIR/sparfile

SMP Turbomole Guidelines
------------------------

Not all Turbomole tools are multi-core, and not all multi-core tools may be 
efficient with large numbers of cores - please refer to the Turbomole guide 
for details. To prevent wasting resource on the HEC, SMP Turbomole should only 
be used for workloads that can efficiently use the number of requested cores.

The template above is intended as an example. The template can be modified 
in the same way as an OpenMP parallel job. Please refer to 
the :ref:`Using OpenMP on the HEC<open mp>`_ page for an explanation of the job 
directives contained in the above templates.

