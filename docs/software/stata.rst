Stata and StataMP
=================

This page covers Stata from version 14 onwards, which is a 
multiprocessor (parallel) version of Stata. Versions on the 
HEC earlier than this are single core (serial), and must be 
run as serial batch jobs without the -mp suffix.

The HEC hosts a 20-user licence for Stata. Starting with 
version 14 this is the 'MP4' 4-core version, providing up 
to a 4x speedup for many Stata commands.

Invoking Stata
--------------

To enable access to the current default version, use the following 
modules command:

.. code-block:: console

   module add stata

This will set up your environment to enable access to the current default 
version of Stata, which may not be the latest version. To instead see the 
full list of available versions, run the command:

.. code-block:: console

  module avail stata

Once the chosen module is added, Stata can then be invoked within a job 
script with the stata-mp command. See the section on 
:ref:`Environment Modules <envmods>` for more details.

A simple serial batch job script for Stata
------------------------------------------

The following is an example of a job script which will run 
stata commands contained in the file filename.do:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial
  #SBATCH --license=stata

  source /etc/profile
  module add stata

  stata -b do filename

Note that the sbatch resource request ``--license=stata`` command is 
required to allow stata to run.

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

Stata-MP8 and Stata-MP16
------------------------

The HEC also hosts 2 licenses for the MP8 and MP16 versions 
of Stata available to researchers in the Econmics Department. 
These enable parallel stata to use up to 8 or 16 cores 
respectively. The parallel job script above can be used a 
template with three alterations:

* The ``--cpus-per-task`` value should be increased to the relevant amount

* The ``--license`` requested should be either ``statamp8`` or ``statamp16``

* The module added should be either the **-mp8** or **-mp16** version 
  (e.g ``module add stata/14-mp8``)
