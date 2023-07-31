Job arrays - Submitting multiple similar jobs
=============================================

For workloads such as Monte Carlo simulations and parameter 
studies, it is often necessary to run the same program multiple 
times, often with slightly different input parameters. Rather 
than create a unique job script for each run and submit each of 
them separately, SLURM offers a *job array* feature. When 
combined with a tailored job script, this allows you to submit 
multiple similar jobs with a single command and a single job script.

Job arrays can be submitted by adding the **-a** directive to the 
job script, as in the following example:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial
  #SBATCH -J array_test
  #SBATCH -a 4-10:2

  source /etc/profile

  echo This is job task ${SLURM_ARRAY_TASK_ID}

  ./my_program < input.${SLURM_ARRAY_TASK_ID}.dat

This splits the workload into a number of *tasks*, with each task 
being a separate job and having its own unique *task id number*. 
This task id number can then be used within the job script to perform 
slightly different actions each time, e.g. reading from a different 
input file (as in the above example), or passing a different set of 
parameters to your program for each task.

The number of tasks and the values of the task id number are controlled 
by the extra arguments following the **-a** directive. The format is **x-y:z**, 
where **x** is the first index number, **y** the last, and the optional **:z** gives 
the step increment. The above example submits the job script 4 times, 
with task id numbers of 4, 6, 8 and 10 (i.e. first = 4, last = 10, step = 2). 

.. note::
  Task id numbers must always be positive integers.

The index number is available to the job script via the environment variable 
*$SLURM_ARRAY_TASK_ID*, and can be used by the job script to alter what 
exactly is run for each task. In the above example, it is used to change 
the name of input file sent to the user application **my_program**. 
Successive tasks will read *input.4.dat*, *input.6.dat*, *input.8.dat* and 
*input.10.dat*.

The standard output and standard error files for each task will be unique; 
by default SLURM will name the output file using the job name, the job ID, 
and an underscore followed by the task ID.

.. tip:: 
  It may look a little limited that the task ID can only take on a positive 
  integer value, as this may not immediately match your requirements - e.g. 
  floating point values or strings. However, job scripts are written 
  in bash, so it normally doesn't take much bash scripting to convert that integer 
  into a more suitable value.

Monitoring job arrays
---------------------

Once you get the hang of writing flexible job scripts, job arrays make 
job submission much easier. They also make job management easier too. 
All tasks within the same job are given a different task id number, 
but all have the same job id. An example output from squeue an array job 
is given below. As with the job submission script format, each task's 
index number appears in the Job ID field:

.. code-block:: console

             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
      1188_[8-100]    serial array_te testuser PD       0:00      1 (Resources)
            1188_1    serial array_te testuser  R       1:10      1 comp17-08
            1188_2    serial array_te testuser  R       0:57      1 comp17-08
            1188_3    serial array_te testuser  R       0:50      1 comp17-08
            1188_4    serial array_te testuser  R       0:43      1 comp17-08
            1188_5    serial array_te testuser  R       0:21      1 comp17-08
            1188_6    serial array_te testuser  R       0:19      1 comp17-08
            1188_7    serial array_te testuser  R       0:07      1 comp17-08

The above example shows a job with 100 tasks. As the cluster is busy, only tasks 
1 through 7 are currently running and the remaining tasks are listed as PD (pending).

If you want to stop all the tasks of a job at once, you can use the normal scancel command, e.g.:

.. code-block:: console

  scancel 1188

If you want to stop individual tasks, you can suffix the job id with the individual 
task id. To stop just task ID 4 of job ID 1188 above, we do:

.. code-block:: console

  scancel 1188_4

To stop the tasks IDs 1-3 of job ID 1188:

.. code-block:: console

  scancel 1188_[1-3]

Managing short jobs
-------------------

Care should be taken to avoid very short jobs - on the order of a 
few seconds to a few minutes - as these make very inefficient 
use of the cluster. It takes the system several seconds both to 
start and finish a job, and the scheduler itself works on 15 second cycles. 
Very short jobs therefore end up causing a lot of idle time on the system. 
To avoid this, consider bunching several short tasks together into a 
single job array element.

The example below gives a template for this type of solution. A job 
array originally of 10,000 individual tasks each of which ran for only 
a few seconds has been converted into one containing just 10 tasks, with 
each task containing a loop to execute the next 1,000 tasks in sequence, 
depending on the job task ID it receives:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial
  #SBATCH -J array_test
  #SBATCH -a 1000-9001:1000

  source /etc/profile

  echo This is job task ${SLURM_ARRAY_TASK_ID}

  x=$SLURM_ARRAY_TASK_ID
  y=$(($SLURM_ARRAY_TASK_ID+$SLURM_ARRAY_TASK_STEP-1))

  echo Running $x to $y

  for z in `seq $x $y`; do
    echo Running task $z
    myprogram < input.$z.data > output.$z.data
  done
