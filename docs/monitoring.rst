Monitoring jobs on the HEC
==========================

Viewing job state, or: Is my job running yet?
---------------------------------------------

The most basic type of job monitoring is to check the job's current
*state*, ie is it running, completed or still pending?

This can be done with the command ``squeue --me``  
and produces output similar to this emample:

.. code-block:: console

    JOBID PARTITION     NAME      USER ST       TIME  NODES NODELIST(REASON)
      930    serial     myjob testuser  R    1:13:19      1 comp17-08
      931    serial     myjob testuser  R       0:02      1 comp17-08
      932    serial     myjob testuser PD       0:00      1 (Resources)
  
The output columns are fairly self-explanatory:

.. list-table::

  * - JOBID
    - A number used to uniquely identify your job within the job scheduling system. Use this 
number when you want to delete or cancel a job via the ``scancel`` command.
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
^^^^^^^^^^^^^

A job's lifecycle can be tracked via the ST (state) field in the output 
from ``squeue``. All jobs start with a status of PD (pending). If the cluster 
is busy, or the job has requested a resource which is currently fully 
utilised, then a job may spend some time in this state.

Once an appropriate job slot is available, the job's status changes 
to R (running). A job just finishing will show as state CG (completing) 
but this state should not last for more than a few seconds. When a job 
no longer appears on the ``squeue`` output, it has either finished or has 
been deleted.

By default, jobs will create two output files containing the output that would
normally appear on the screen when run in non-batch mode, and corresponding
to the linux *stdout* and *stderr* channels. Output files names are a combination
of the *job name*, the *job id* and the I/O channel name. So a job named **mytest**
submitted as job id **1234** would create output files named **mytest.1234.out** and
**mytest.1234.err**

Job output files are created once the job starts running, though they will
initially be empty and will slowly fill with output. These files will be simple
text files and can viewed using standard file paging commands such as ``less``

.. note::

  The job output files will collect any output not collected by other means.
  Some applications may save their output into separate files - check the user
  guide for your application to see how it behaves.
  
.. note::

  All files written to in a batch job use *buffering* in order to make efficient use
  of the file system. This means that you won't see any output in your files straight away.
  Once there's 8 kilobytes of output - or once a *process* within your job finishes -
  the buffer contents will be written to the relevant output file.

.. tip::
  If ``squeue`` doesn't show a job you expect to be in the queue, check to see 
  if the job's output files exist. For very short jobs (including those 
  that stop quickly due an error) it's common for the job to transition 
  from pending to running to completed before you've had chance to view it 
  with ``squeue``.

Email notification of job completion
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Rather than repeatedly running ``squeue`` to check the state of your jobs, 
you can opt to receive email notification when your jobs complete by adding 
the following line to your job submission command:

.. code-block:: console

  --mail-type=END,FAIL --mail-user=youraddress@lancaster.ac.uk

Alternatively, you can add the following lines to your job submission script:

.. code-block:: bash

  #SBATCH --mail-type=END,FAIL
  #SBATCH --mail-user=youraddress@lancaster.ac.uk

The email will contain a summary of the resources used by your job, for example:

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
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When applied to :doc:`job arrays</jobarrays>`, the mailback option would result in a 
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


Viewing your job resource quota with qquota
-------------------------------------------

To ensure a fair share of the cluster, each user is capped by a set of 
*resource quotas* implemented using SLURM's QoS (Quality of Service) 
feature. Jobs submitted to the cluster are eligible to run provided 
they don't cause the user's resource usage to exceed their current quota. 
In cases where job start would cause a resource quota to be breached the 
job is held waiting until the user resource usage has reduced by enough 
capacity to support it - typically by waiting for other running jobs to complete.

Currently two resource quotas are enforced:

**Job slots** have a quota of 384 (i.e. a user may have running jobs consuming 
a total of up to 384 job slots or cores)

**Memory usage** is capped at a total of 1.84TB (i.e. users may have running jobs 
totalling up to 1.84TB of memory reservations, which with a job slot quota of 384, 
averages 4.8GB per job slot). Please refer to :doc:`/largemem`
for an explanation of job memory reservation requests.

.. note::

   Users granted access to the HEC as an exception to the usual access policy will have
   smaller quotas than the examples given.

Resource quotas can be viewed using the ``qquota`` command:

.. code-block:: console

  wayland-2022% qquota

  QOSname        Cores           Memory(GB)
   normal       64/384             512/1844
    
Note that if you haven't run any jobs recently then the output will be blank, as 
no QoS record will exist for you.

Monitoring job efficiency
-------------------------

Job resource requests such as the number of cores or the amount of
memory are *reservations*, much like reserving a table at a
restaurant. That means the requested resources are reserved for that
job whether or not the job makes full use of them. It's important to
keep tabs on your jobs' resource usage to make sure that resource
requests made by jobs are accurate; having a large number of inaccurate resource
requests on the cluster will result in the cluster becoming starved of
those resources for waiting jobs, even though the currently running
jobs aren't using them.

There are several tools to monitor how a job is - or has been - using
resources, with different tools allowing for monitoring of jobs once
they've completed or while they're still running.

Monitoring of completed jobs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Summaries of completed jobs are stored in a database, which can be 
interrogated via the ``sacct`` command. The database structure is 
complex, so it's often best to view job summaries via helper scripts 
which use ``sacct`` under the bonnet as described below. You can
still access the ``sacct`` command directly - see ``man sacct`` for
details on how its used.

Job resource usage summaries via seff
#####################################

The job resource usage summary shown in the mailback notification 
for job completion above can also be run at any time via the ``seff`` 
helper script. E.g. for job ID 1168, which runs a serial (single CPU) 
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
memory resource request should be have been lower. (Note that the job was 
manually stopped via the scancel command after half an hour, hence 
the job state of CANCELLED).

Job summaries via qacct
#######################

The ``qacct`` command acts as a wrapper to ``sacct`` and extracts 
only key job information. Using the previous job as an example, 
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
  
The output provides basic information such as the job name, submit-,
start- and end-timestamps, and the resources requested.  Additional
fields can be added using the ``-o`` option which is passed on to the
underlying call to ``sacct`` (see the ``sacct`` man page for details
of the ``-o`` option. Note that the ``qacct`` excludes information on
job steps, so some fields may be empty).

Monitoring running jobs
^^^^^^^^^^^^^^^^^^^^^^^

As jobs can run for several hours or days, it's useful to see how jobs
are running - and what they're running - in order to spot any potential
problems in a job as early as possible. This is especially useful when
running any new type of workload - either a new application, or a
different model within an existing application. The commands ``qcgtop``
and ``qtop`` can help with this monitoring.


Monitoring jobs with qcgtop
###########################

The ``qcgtop`` command will show a summary of current CPU and memory
usage for your jobs. Each Slurm job is managed by a Linux *control
group*, which on a typical Linux desktop or server can be viewed via
the ``systemd-cgtop`` command. The ``qcgtop`` command uses this
information or provide a job resource usage summary.

Consider the following job output from ``squeue --me``, which shows a 
2-node parallel job running:

.. code-block:: console

      JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
       1142  parallel imb-32wa testuser  R       0:03      2 comp17-[08-09]

The current amount of memory and CPU resource being consumed by the 
running job can be viewed vie the command

.. code-block:: console

  wayland-2022% qcgtop -u testuser

         Job   %CPU Memory
         ---   ---- ------
  comp17-08
    job_1142 1592.8   1.3G 
  comp17-09
    job_1142 1590.8   1.3G

The output shows the CPU and memory utilisation of each job on each
node.  The CPU usage reported in this example is close to 1600%, which
is the expected value for parallel jobs fully utilising all CPUs on a
16-core compute node.

Monitoring jobs with qtop
#########################

While the ``qcgtop`` tool described above provides an overall summary
of each jobs' CPU and memory usage, it doesn't provide a breakdown of
the individual processes within a job. The ``qtop`` tool can be used
to view the individual processes within jobs - along with each process'
memory and CPU utilisation. The drawback with ``qtop`` is that it
isn't job-aware and will simply display each process being run on each
compute node.

As an example of its usage, consider the following job list for user
*testuser* running the command ``squeue --me`` to view their jobs:

.. code-block:: console

             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
              2286 serial    nbody2.s testuser  R       0:01      1 comp01-02
              2285 serial    nbody.sb testuser  R       0:16      1 comp01-01
              2284 serial    nbody.sb testuser  R       3:43      1 comp01-01

The output shows that *testuser* has three jobs running across two compute nodes:
*comp01-01* and *comp01-02*. The result of running ``qtop -u testuser`` looks like
this:

.. code-block:: console

  Host: comp01-01
      PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  1235678 testuser  20   0 7077748   1.2g 131936 R 100.0   0.6   3:46.31 nbody
  1235915 testuser  20   0 7077748   1.2g 132180 R 100.0   0.6   0:19.46 nbody
  1235961 testuser  20   0   50120   4508   3600 R   1.0   0.0   0:00.01 top
  1235622 testuser  20   0   15268   3608   3156 S   0.0   0.0   0:00.00 slurm_s+
  1235859 testuser  20   0   15268   3608   3156 S   0.0   0.0   0:00.00 slurm_s+
  1235960 testuser  20   0  141276   5404   3912 S   0.0   0.0   0:00.00 sshd
  Host: comp01-02
      PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  1229929 testuser  20   0   10.8g   1.5g 954020 R  99.0   0.8   0:05.46 nbody
  1229873 testuser  20   0   15268   3596   3140 S   0.0   0.0   0:00.00 slurm_s+
  1229944 testuser  20   0  141276   5524   4032 S   0.0   0.0   0:00.00 sshd
  1229945 testuser  20   0   50120   4476   3560 R   0.0   0.0   0:00.01 top

The output fields for processes are identical to those for the
standard linux ``top`` command executed in batch mode - see the man
page for an in-depth description of the meaning of each field. This
description will cover only the more relevant fields. Sets of
processes are grouped so that all of a user's processes on a compute
node appear together.

The first thing to note is that the information provided by ``qtop``
is very different from that of ``squeue``. ``qtop`` is not an
integrated part of SLURM so it will output process information from
each compute node with a running job, rather than job information - a
single job will involve executing a number of processes on a compute
node. You'll need to compare ``qtop`` and ``squeue`` output to work
out just what's going on. For example, ``qtop`` doesn't give you the
job-ID number, and it often lists two or more processes where
``squeue`` or ``qcgtop`` lists just one job.

The three most relevant fields in the output are labelled **COMMAND**,
**RES** and **CPU**.

The **COMMAND** field shows the name of the command being run by the
process. Because jobs are submitted to the cluster as a job the job
script itself becomes a process, which is named slurm_script,
shortened to **slurm_s+** in the above output.  The **slurm_script**
typically consumes very little CPU - it's simply setting up the job's
working environment and then calling the applications requested in the
job submission script.

As the ``qtop`` command runs the standard Linux ``top`` command on
each compute node, this command will also appear in the list along
with an ssh process (labelled **sshd**) which enables the remote
command.

For most purposes, you'll be interested in the remaining process(es)
listed - typically the main process that your job script is currently
running. In the above example the remaining processes are all called
**nbody** - one of the applications available on the HEC and the main
command in submitted job scripts.

The **RES** field gives the total *resident memory* size of each
process.  Smaller process sizes are listed in (k)ilobytes, larger ones
in (m)egabytes, or even (g)igabytes.

The other useful field in the qtop output is **CPU**, which describes
how much of a single CPU the process is consuming. Typically a running
serial job should be consuming very close to 100% of a CPU's
resources. In contrast, an MPI parallel job will show multiple
processes, each consuming around 100% CPU. OpenMP and other
multi-threaded processes will show a single process entry consuming
several hundred percent CPU - ideally 100x the number of cores being
used. Values considerably lower than these ideals will likely indicate
some problem; the process might be spending a disproportionate amount
of time performing file reads or writes; or in the case of badly
balanced parallel programs one process might be idle while waiting for
a communication from another process.

Note that the **PID** field gives the Linux process ID, not the SLURM
Job ID. Each process on a Linux system is assigned a unique process
ID, which forms part of the standard output for top.

