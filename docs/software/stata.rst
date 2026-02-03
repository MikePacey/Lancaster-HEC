Stata and StataMP
=================

Invoking Stata
--------------

To enable access to the current default version, use the following 
``modules`` command:

.. code-block:: console

   module add stata

This will set up your environment to enable access to the current default 
version of Stata, which may not be the latest version. To instead see the 
full list of available versions, run the command:

.. code-block:: console

  module avail stata

Once the chosen module is added, Stata can then be invoked within a job 
script with the ``stata``, ``stata-se`` or ``stata-mp`` commands. See the section on 
:doc:`/envmods` for more details.

A simple serial batch job script for Stata
------------------------------------------

The following is an example of a job script which will run 
stata commands contained in the file `filename.do`:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial

  source /etc/profile
  module add stata

  stata -b do filename

A simple parallel job script for Stata
--------------------------------------

The current stata versions on the HEC are Stata-MP4, allowing up to 4 cores 
to be used to speed up processing. If your stata tasks can be run in parallel, 
a 4-core stata job can be submitted using the following template:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH --cpus-per-task=4

  source /etc/profile
  module add stata

  stata-mp -b do filename

Notes on parallel Stata:
-----------------------

Not all Stata functions are able to run in parallel, and not all parallel functions
scale to the maximum possible number of cores. Parallel stata jobs should be tested
carefully to ensure that requested cores don't go to waste. If your workload parallelises
to a smaller core count, then the ``--cpus-per-task`` resource request can be changed
to a smaller value.

