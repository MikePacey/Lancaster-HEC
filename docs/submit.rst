Job Submission on the HEC
=========================

The HEC login node acts as the interface between you and the HEC 
proper (the cluster of compute nodes). Rather than running 
compute-intensive workloads directly on the login node, they 
must be submitted from the login node to the SLURM scheduling 
system as *job scripts* - a text file containing the commands 
to be run along with the compute resources required to run them. 
The scheduler will match each job's *resource requests* (such 
as memory and number of CPUs) to available resources, ensuring 
that users each get a fair share of the cluster.

The two basic types of job are Batch and Interactive, submitted 
via the ``sbatch`` and ``srun`` commands described below. 
Computationally intensive and/or large memory jobs must NOT be 
run directly on the login node; the login node is a shared 
server which has limited resources and should be used for 
lightweight tasks such as job submission, pre- and post-processing 
of job data and compilation.

.. Tip:: 
  If you need to run small tests on jobs before submitting them, 
  please use either the test queue or an interactive job session.

Batch jobs
----------

A batch job is one which can be run without user intervention 
- i.e. it does not require any input from the keyboard and does 
not send any output to the user's screen. Typically a batch 
job will read any input it needs from a pre-written file, and 
send its output to files in the user's directory. The exact 
methods of doing this depend on each application. The majority 
of jobs submitted to the HEC are batch jobs, as it allows 
users to run large numbers of jobs at the same time without 
needing constant user input.

Batch jobs are run on the HEC by creating a batch job script 
(or command file) and submitting it to the system using the 
command sbatch. For example a job script in a file named 
*my_program.com* can be submiited with the command:


.. code-block:: console

  sbatch my_program.com

Assuming that the resources requested by the job are available, 
the job scheduler will select a compute node on which to run your 
job. This ensures that the combined load of all users' jobs is 
spread evenly over the entire cluster without overloading any one 
compute node. If insufficent resources are available at the time 
then the job will be held in a pending state until the resources 
become free. To see how busy the HEC is, use the ``qslots`` command, 
which reports the number of available job slots.

The cluster uses a Fair Share scheduling policy; users may submit 
any number of jobs and jobs over a certain number will be held pending, 
while priority will be given to those who are currently running 
fewer jobs. Please check the login node's message of the day for any 
changes to scheduling.

An example batch job script
---------------------------

The following is a simple batch job script which can be cut-and-pasted
directly into a text editor:

.. code-block:: sh

  #!/bin/bash

  #SBATCH -p serial
  #SBATCH -J testjob

  source /etc/profile

  echo Job running on compute node `uname -n`


Explanation of batch job scripts
--------------------------------

Batch job scripts are simply standard bash shell scripts with a few extra 
lines (beginning with *#SBATCH*) containing instructions for the scheduler. 
The first line of the job script:

.. code-block:: bash

  #!/bin/bash

instructs the scheduler to run the job using the **bash** shell. This is strongly 
recommended as best practice - all job templates and examples on these pages 
are written using bash.

The next line:

.. code-block:: bash

  #SBATCH -p serial

directs the job to the serial *partition* (sometimes referred to as a *queue*). 
This partition is intended for running single-core (serial) jobs, and should 
be the default queue for many types of jobs. Different partitions exist to support 
more advanced job types, such as parallel jobs or those which require specialised
node types.

The next line:

.. code-block:: bash

  #SBATCH -J myjobname

sets a name for your job, so that you can easily identify it while it's running. 
If you omit this option the job scheduler will simply use the filename of the job script
as the job name. The job name will also be used to create the job output files (see below).

The final job setup line reads:

.. code-block:: bash

  source /etc/profile

This will set up the bash shell environment of the job so that it matches the 
functionality you see on the login node.

Once the batch job environment has been specified, subsequent lines should 
contain the commands needed to run your job. The job will effectively run 
as a bash shell script, and will process any of the usual commands permitted 
from the specified shell. The example command above is:

.. code-block:: bash

  echo Job running on compute node `uname -n`

which simply prints a short message to say which compute node the job was 
run on. See the Software section of this guide for templates of job 
scripts for popular packages.

.. tip::

   The HEC login node offers a range of different text editors which can be used
   to write jobs scripts. Available text editors include ``nano`` (a simple editor),
   ``vim`` and ``emacs`` (both feature-rich editors with broad user bases). Writing
   job scripts directly on the HEC is often much more convenient than writing them on
   your desktop and transferring the files across.

Interactive jobs
----------------

While batch jobs are the most efficient type of job to submit, 
some applications may require regular user input making them 
unsuitable for batch job submission. In such cases, jobs can 
be submitted interactively, giving you a command line shell 
on a compute node with sufficient free resources to run your 
application. You can submit an interactive job with the 
following command:

.. code-block:: bash

  srun --pty bash -i

If the interactive job request can be satisfied your shell prompt 
will immediately change to the name of the compute node assigned 
to the job. E.g.:

.. code-block:: console

  wayland-2022% srun --pty bash -i
  comp17-08%
  
If the requested resources aren't available, you'll see a message 
to that effect and your interactive job will wait until resources 
become available. E.g:

.. code-block:: console

  wayland-2022% srun --pty bash -i
  srun: job 544 queued and waiting for resources

.. note::
  Don't forget to log out from your interactive session when you 
  have finished your tasks - your job slot and any resources it 
  reserves are not available to anyone else until you do so.

The test queue
--------------

The test queue exists to allow quick-turnaround testing of jobs 
during times when the cluster is otherwise busy by dedicating a 
single compute node for this purpose. It can be frustrating 
to wait a few hours for a job to launch on a busy cluster only 
to have it fail immediately on launch due to a typo in the job 
submission script, so the test queue can help spot errors that 
occur early on in a job. If you want to do a quick sanity check 
of a new or altered job submission script, or if you want to 
try out some small jobs to get the hang of the job submission 
system, then the test queue is recommended.

To use this queue, simply add ``-p test`` to your ``sbatch`` job submission 
command to divert the job to the test partition (queue). This queue is 
usually lightly loaded, and should give very fast turnaround.

To ensure fast turnaround, jobs submitted to the test queue are limited 
to a maximum of 5 minutes run time. Jobs running for more than 5 minutes 
in this queue will be automatically terminated.

.. note::
  The test queue is available only on a single dedicated compute node which 
  has 16 cores, 64G of memory and node_type 10Geth64G. When testing, make 
  sure that your job resource requests can match this.
