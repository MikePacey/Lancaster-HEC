Quick Reference (Cheat Sheets)
==============================

Login and data transfer
-----------------------

.. list-table::
 
  * - *ssh USERNAME@wayland.hec.lancs.ac.uk*
    - login from a linux desktop
  * - *scp -r /path/to/my/directory USERNAME@wayland.hec.lancs.ac.uk*
    -  upload a directory to the HEC from a linux desktop 
  * - *scp -r /path/to/my/directory USERNAME@wayland.hec.lancs.ac.uk*
    - upload a directory to the HEC from a linux desktop
  * - *smbclient -k -D folder-path //luna/faculty-code*
    - connect to research folder on LUNA. User put and get to upload/download

Job Submission
--------------

.. list-table::

  * - *sbatch JOBSCRIPT*
    - submit JOBSCRIPT as a batch job job
  * - *srun -\-pty bash -i*
    - submit an interactive job

Job Submission Options
----------------------

Note: In a job script, job option lines start with #SBATCH

.. list-table::

  * - *-p QUEUENAME*
    - submit job to *QUEUENAME* - e.g., serial, parallel, test, astro
  * - *-\-mem=SIZE*
    - request *SIZE* memory for a job

.. list-table::

  * - -\-nodes=N

      -\-exclusive

      -\-node_type=NODETYPE

Job Monitoring
--------------


