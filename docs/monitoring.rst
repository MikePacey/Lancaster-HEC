Monitoring jobs on the HEC
==========================

Viewing your submitted jobs with squeue
---------------------------------------

The simplest way to monitor your HEC jobs is with the 
command ``squeue --me``. This will output a list of all 
your jobs currently running or waiting to run on the cluster:

.. code-block:: console

    JOBID PARTITION     NAME      USER ST       TIME  NODES NODELIST(REASON)
      930    serial     myjob testuser  R    1:13:19      1 comp17-08
      931    serial     myjob testuser  R       0:02      1 comp17-08
      932    serial     myjob testuser PD       0:00      1 (Resources)
  
The output columns are fairly self-explanatory:

.. list-table::

  * - JOBID
    - A number used to uniquely identify your job within the job scheduling system. Use this number when you want to terminate a job via the scancel command.
  * - PARTITION
    - The name of the partition (queue) the job was submitted to
  * - NAME
    - The job's name, as specified by the job submission script's *-J* directive, or the filename of the job script if a name wasn't assigned
  * - USER
    - The user (account) name of the job owner
  * - ST
    - The state of the job, e.g. R for running or PD for pending
  * - TIME
    - The amount of time the job has been running for in the format hh:mm:ss
  * - NODE
    - The number of compute nodes requested for a parallel job. Serial jobs or parallel jobs that don't use all cores on a compute node will show a value of 1
  * - NODELIST(REASON)
    - For running jobs this is the list of compute nodes the job is running on. For pending jobs this will give the reason for the pending status (e.g. Resources if the cluster is busy, or Priority if other pending jobs have a higher priorty to run

The default action for ``squeue`` is to output basic information on all jobs. 
The list of all users' jobs is usually very long!

Job Lifecycle
-------------

A job's lifecycle can be tracked via the ST (state) field in the output 
from ``squeue``. All jobs start with a status of PD (pending). If the cluster 
is busy, or the job has requested a resource which is currently fully 
utilised, then a job may spend some time in this state. (For more 
information on requesting resources, please refer to Requesting 
specific node types for jobs on the HEC.)

Once an appropriate job slot is available, the job's status changes 
to R (running). A job just finishing will show as state CG (completing) 
but this state should not last for more than a few seconds. When a job 
no longer appears on the **squeue** output, it has either finished or has 
been deleted.

By default, jobs will create two output files containing the output that would
normally appear on the screen when when in non-batch mode, and corresponding
to the linux *stdout* and *stderr* channels. Output files names are a combination
of the *job name*, the *job id* and the channel name. So a job named *mytest*
submitted as job id *1234* would create output files named *mytest.1234.out* and
*mytest.1234.err*

Job output files are created once the job starts running, though they will
initially be empty and will slowy fill with output.

.. note::

  The job output files will collect any output not collected by other means.
  Some applications may save their output into separate files - check the user
  guide for your application to see how it behaves.
  
.. note::

  All files written to in a batch job use buffering in order to make efficient use
  of the file system. This means that you won't see any output in your files straight away.
  Once there's 8 kilobytes of output - or once a *process* within your job finishes -
  the buffer contents will be written to the relevant output file.

.. tip::
  If ``squeue`` doesn't show a job you expect to be in the queue, check to see 
  if the job's output files exist. For very short jobs (inlcuding those 
  that stop quickly due an error) it's common for the job to transition 
  from pending to running to completed before you've had chance to view it 
  with squeue**.

Viewing your job resource quota with qquota
-------------------------------------------

To ensure a fair share of the cluster, each user is capped by a set of 
resource quotas implemented using SLURM's QoS (Quality of Service) 
feature. Jobs submitted to the cluster are eligible to run provided 
they don't cause the user's resource usage to exceed their current quota. 
In cases where job start would cause a resource quota to be breached the 
job is held waiting until the user resource usage has reduced by enough 
capacity to support it - typically by waiting for other running jobs to complete.

Currently two resource quotas are enforced:

**Job slots** have a quota of 350 (i.e. a user may have running jobs consuming 
a total of up to 350 job slots / cores)

**Memory usage** is capped at a total of 1.64TB (i.e. users may have running jobs 
totalling up to 1.64TB of memory reservations, which with a job slot quota of 350, 
averages 4.8GB per job slot). Please refer to :doc:`/largemem`
for an explanation of job memory reservation requests.

.. note::
   Users granted access to the HEC as an exception to the usual access policy will have
   smaller quotas than the examples given.

Resource quotas can be viewed using the ``qquota`` command:

.. code-block:: console

  wayland-2022% qquota

  QOSname        Cores           Memory(GB)
   normal       64/350             512/1640
    
Note that if you haven't run any jobs recently then the output will be blank, as 
no QoS record will exist for you.

Email notification of job completion
------------------------------------

Rather than repeatedly running ``squeue`` to check the state of your jobs, 
you can opt to receive email notification when your jobs complete by adding 
the following line to your job submission command:

.. code-block:: console

  --mail-type=END,FAIL --mail-user=youraddress@lancaster.ac.uk

Alternatively, you can add the following lines to your job submission script:

.. code-block:: bash

  #SBATCH --mail-type=END,FAIL
  #SBATCH --mail-user=youraddress@lancaster.ac.uk

The email will contain a summary of the resources used by your job:

.. code-block:: console

  Job ID: 1140
  Cluster: hec-main
  User/Group: testuser/local
  State: COMPLETED (exit code 0)
  Nodes: 2
  Cores per node: 16
  CPU Utilized: 00:00:30
  CPU Efficiency: 3.23% of 00:15:28 core-walltime
  Job Wall-clock time: 00:00:29
  Memory Utilized: 3.54 MB
  Memory Efficiency: 0.00% of 121.09 GB
  Email notification for job arrays

When applied to job arrays, the mailback option would result in a 
notification for every completed array element - so a 10,000 element 
job array will result in 10,000 email notifications. To prevent 
overloading the mail system, job arrays with the mailback option set 
will be rejected at submission time.

If you'd like to be notified when a job array finishes, create a dummy 
job (i.e. one which does very little work) with the email notification 
commands above, and make it dependent on the completion of the job array 
by adding the command line arguments ``-d jobid`` to sbatch, where *jobid* 
is the job ID of the job array. This will cause the dummy job to wait until 
all elements of the specified job array have finished before it runs - 
it will then run for a few seconds, complete, and email you.

Monitoring jobs with qcgtop
---------------------------

The ``squeue`` command described above gives basic information about the 
status of a job. Sometimes though, it's useful to have a more dynamic 
look at how well a job is running. For example, to see how much memory 
a job is using when running, or to check that it hasn't stalled.

Each slurm job is managed by a Linux *control group*, which on a typical 
Linux desktop or server can be viewed via the ``systemd-cgtop`` command. 
On the HEC, user jobs' cgroup info can be viewed using the ``qcgtop`` command.

Consider the following job output from ``squeue --me``, which shows a 
2-node parallel job running:

.. code-block:: console

      JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
       1142  parallel imb-32wa testuser  R       0:03      2 comp17-[08-09]

The current amount of memory and CPU resource being consumed by the 
running job can be viewed vie the command

.. code-block:: console

  wayland-2020% qcgtop -u testuser

         Job   %CPU Memory
         ---   ---- ------
  comp17-08
    job_1142 1592.8   1.3G 
  comp17-09
    job_1142 1590.8   1.3G

The output shows the CPU and memory utilisation of each job on each node. 
The CPU usage reported in this example is close to 1600%, which is the 
expected value for parallel jobs fully utilising all CPUs on a 16-core compute node.

Reviewing logs of completed jobs
--------------------------------

A summary of completed jobs are stored in a database, which can be 
interrogated via the ``sacct`` command. The database structure is 
complex, so it's often best to view job summaries via wrapper scripts 
which use ``sacct`` under the bonnet as described below.

Job resource usage summaries via seff
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The job resource usage summary shown in the mailback notification 
for job completion above can be run at any time via the ``seff`` 
script. E.g. for job ID 1168, which runs a serial (single CPU) 
benchmark for the Yank free energy calculation framework the 
command ``seff 1168`` produces this output from a serial job:

.. code-block:: console
  Job ID: 1168
  Cluster: hec-main
  User/Group: testuser/local
  State: CANCELLED (exit code 0)
  Cores: 1
  CPU Utilized: 00:31:12
  CPU Efficiency: 99.47% of 00:31:22 core-walltime
  Job Wall-clock time: 00:31:22
  Memory Utilized: 2.10 GB
  Memory Efficiency: 42.07% of 5.00 GB

The output shows that CPU utilisation was very high (close to 100%), 
so good use was made of the requested CPU resource. 
Memory utilisation however was below 50%, suggesting that the job's 
memory resource request should be lower. (Note that the job was 
manually stopped via the scancel command after half an hour, hence 
the job state of CANCELLED).

Job summaries via qacct
~~~~~~~~~~~~~~~~~~~~~~~

The ``qacct`` command acts as a wrapper to ``sacct`` and extracts 
relevant job information. Using the previous job as an example, 
we can run:

.. code-block:: console

  qacct -j 1168

Which produces the output:

.. code-block:: console

  JobID      1168
  JobName    yank-serial.sb
  Partition  serial
  User       testuser
  Submit     2022-12-12T11:07:08
  Start      2022-12-12T11:07:08
  End        2022-12-12T11:38:30
  ExitCode   0:0
  State      CANCELLED by testuser
  AllocTRES  billing=1,cpu=1,mem=5G,node=1
  NodeList   comp17-08
  
The output provides basic information such as the job name, 
submit-, start- and end-timestamps, and the resources requested. 
Additional fields can be added using the ``-o`` option which is passed 
on to the underlying call to ``sacct`` (see the ``sacct`` man page 
for details of the **-o** option. Note that the ``qacct`` excludes 
information on job steps, so some fields may be empty).
