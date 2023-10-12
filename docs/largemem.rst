Running large memory jobs on the HEC
====================================

The job scheduler cannot predict how much memory your 
jobs will consume, so it is important for you to declare 
the amount required when submitting a job. Specifying the 
correct amount means that the scheduler can ensure that 
jobs are sent to a compute node with enough memory to support 
them. By default, jobs are assigned a memory resource request 
of 500M. Jobs over 500M in size are classed as large memory 
jobs and must specify their memory requirements.

It's important to declare memory usage as accurately as 
possible - the job scheduler reserves that memory for you 
whether you use it or not, so specifying more memory than 
you need quickly leads to memory starvation across the cluster, 
limiting other users from running their jobs. 
Bear in mind that in order to guard against jobs with run-away 
memory leaks which might adversely affect other users the 
scheduler will terminate any job which exceeds its requested 
memory requirement.

Specifying a job's memory resource requirement is a delicate 
balance - enough to enable the job to run, but not so much 
higher than is geniunely needed in order to avoid wasting 
resource. If you're uncertain how much memory a new type of 
job requires, we recommend submitting a single job with a 
very large memory requirement, then using the monitoring tools 
(e.g. seff) to find the actual memory requirements once it has 
completed so that you can specify a more accurate memory resource 
requests for future jobs.

Adding memory resource requests to jobs
---------------------------------------

For jobs greater than 500 megabytes, calculate the amount of 
memory your job requires as closely as possible and submit the job with:

.. code-block:: console

  sbatch --mem=X myjob.com

Where X is the amount memory to be reserved along with the unit - 
either M (megabytes) or G (gigabytes). E.g. 700M means 700 megabytes, 
2G means 2 gigabytes. Note that the numeric value must be an integer,
so if you need, e.g., 1.5G then you need to specify 1500M.

Alternatively, you can add a similar directive into the job script 
by adding the line:

.. code-block:: bash

  #SBATCH --mem=2G
