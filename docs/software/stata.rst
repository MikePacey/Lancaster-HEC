Stata and StataMP
=================

The HEC hosts an unlimted network licence version of Stata MP4, along
with 2 licenses each for Stata MP8 and Stata MP16 available
for members of the Economics department.

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
stata commands contained in the file filename.do:

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
  #SBATCH --license=stata

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

Stata-MP8 and Stata-MP16
------------------------

The HEC also hosts 2 licenses each for the MP8 and MP16 versions 
of Stata available to researchers in the Econmics Department. 
These enable parallel stata to use up to 8 or 16 cores 
respectively. 

For Stata MP8, an example script is:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH --cpus-per-task=8
  #SBATCH --license=statamp8

  source /etc/profile
  module add statamp8

  stata-mp -b do filename

While for Stata MP16, an example script is:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH --cpus-per-task=16
  #SBATCH --license=statamp16

  source /etc/profile
  module add statamp16

  stata-mp -b do filename

Note that these versions need the ``--license`` resource request for the approriate
version to ensure that no more than 2 copies of each are running at once. If all
licenses are in use, job will help pending until a licence - and the other requested
resources - are available.
