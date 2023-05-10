The R Stats Package
===================

To access the default version of R on the HEC, simply type the command:

.. code-block:: console

  module add R

R can now be invoked in your session by simply typing ``R``.

Using the module's primary name on its own will result in the 
defalt version of R being used. To see what other versions of 
R are currently available, type:

.. code-block:: console

  module avail R

See the Using environment modules on the HEC page for more details on the module command.

A sample batch job script for R
-------------------------------

R batch jobs may be run by creating a batch job control script 
(for instance, called R_job.sb) like the following:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial
  #SBATCH -J myRjob
  #SBATCH --mem=1G
  
  source /etc/profile
  module add R

  Rscript myscript.R

where **myscript.R** is the name of the file containing the R 
script you want to run. The job script is then submitted for 
running by typing:

.. code-block:: console

  sbatch R_job.sb

Further Reading
---------------

The R `Home Page <https://www.r-project.org/>`_
