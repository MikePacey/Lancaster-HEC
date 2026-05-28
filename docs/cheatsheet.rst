Quick Reference (Cheat Sheet)
==============================

.. note::

   This page is a quick reference guide to be used once you are familiar 
   the HEC User Guide

Login, filestore and data transfer
----------------------------------

.. list-table::
 
  * - *ssh USERNAME@wayland.hec.lancs.ac.uk*
    - :ref:`login<login>` from a linux desktop
  * - *gpfsquota*
    - show usage for for HEC :ref:`filestores<filestore>`
  * - *scp -r /path/to/my/directory USERNAME@wayland.hec.lancs.ac.uk*
    -  :ref:`upload<scp>` a directory to the HEC from a linux desktop 
  * - *scp -r /path/to/my/directory USERNAME@wayland.hec.lancs.ac.uk*
    - :ref:`upload<scp>` a directory to the HEC from a linux desktop
  * - *smbclient -k -D folder-path //luna/faculty-code*
    - :ref:`connect to a research folder<smbclient>` on LUNA. Use put and get to upload/download

Job Submission
--------------

.. list-table::

  * - *sbatch JOBSCRIPT*
    - submit file *JOBSCRIPT* as a :ref:`batch job<batch jobs>`
  * - *srun -\-pty bash -i*
    - submit an :ref:`interactive job<interactive jobs>`
  * - *scancel JOBID*
    - cancel job number JOBID

Job Submission Options
----------------------

Note: In a job script, job option lines start with #SBATCH

To add: #SBATCH lines

.. list-table::

  * - .. code-block:: console
         -p serial
         --mem=SIZE
    - a :ref:`serial job<serial job>` requesting *SIZE* memory (eg. 1G = 1 gigabyte)
  * - .. code-block:: console
         -p parallel
         --cpus-per-task=N
         --mem=SIZE
    -  a :ref:`single node parallel job<open mp>` with N cores on the same compute node, and SIZE memory
  * - .. code-block:: console
         -p parallel
         --nodes=N
         --exclusive
         -C node_type=X
    - a :ref:`multi node parallel job<mpi jobs>` requesting N nodes of compute node type :ref:`X<node types>`
  * - .. code-block:: console
         -p gpu-short
         --gres=gpu:1
         --mem=SIZE
         --cpus-per-task=N
    - submit a :ref:`gpu job<gpu jobs>` to the gpu-short queue, requesting N cores, SIZE memory and a single GPU

Job Monitoring
--------------

.. list-table::

  * - *squeue -\-me*
    - Shows all :ref:`running and pending jobs<squeue>`
  * - *qcgtop -u USERNAME*
    - Shows :ref:`CPU and memeory usage summaries<qcgtop>` for all running jobs
  * - *qtop -u USERNAME*
    - Shows :ref:`top-style information<qtop>` on all running jobs
  * - *qquota*
    - Shows :ref:`job resource quotas<qquota>` (CPUs, memory and GPUs)
  * - *qacct -j JOBID*
    - Shows :ref:`summary of completed<qacct>` job JOBID

Enviroment Modules
------------------

.. list-table::

  * - *module avail*
    - show all :ref:`modules<envmods>`
  * - *module av PATTERN*
    - show available :ref:`modules<envmods>` matching PATTERN
  * - *module add MODULENAME*
    - add MODULENAME to the current shell environment
  * - *module rm MODULENAME*
    - remove MODULENAME from the current shell environment
  * - *module purge*
    - remove all :ref:`modules<envmods>` from the current environment
