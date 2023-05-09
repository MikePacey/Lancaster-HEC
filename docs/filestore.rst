Using Filestore on the HEC
==========================

Overview of filestore
---------------------

User accounts on the HEC have three high performance 
personal filestore areas - home, storage and scratch - 
which are available across the cluster. Each job also 
has access to a unique temp directory. These areas differ 
in size, backup policy and file retention policy.

The **home** area is subject to a small (10G) quota. The home 
area is backed up nightly for 90 days, and should be used 
for essential files (e.g. job submission scripts and your own codes).

The **storage** area has a larger quota (100G) but is not backed up. 
This area should typically be used to store job outputs, your 
own applications, or other research data that can be regenerated if lost.

The **scratch** area is a large (10T) shared area; files written 
to it are automatically deleted after four weeks weeks. The 
scratch area is intended as a place to hold very large job output 
files and other data files for a limited period of time - files 
will be deleted from scratch four weeks after they were last written to.

Finally, the **temp** area is a unique directory available to each job. 
The directory exists on the local disk of the compute node running the 
job, and is intended to provide fast file I/O for jobs which create 
temporary files during their run. The contents of temp are automatically 
deleted at the end of each job, so any essential files created here 
should be copied into one of the home, storage or scratch areas before
 the job terminates.

A summary of the different filestores area is given below:

.. list-table:: Title
   :header-rows: 1

   * - File Area	
     - Quota
     - Backup
     - Policy
     - File Retention policy
   * - home
     - 10G
     - Nightly for 90 days
     - Permanent
   * - storage
     - 100G
     - None
     - Permanent
   * - scratch
     - 10T
     - None
     - Files automatically deleted after 4 weeks
   * - temp
     - Unlimited
     - None
     - Files automatically deleted at the end of the job

Home, storage, scratch and temp can be quickly referenced via the environment 
variables **HOME**, **global_storage**, **global_scratch** and **TMPDIR**
respectively. For example, to quickly change directories to your scratch area, 
type: *cd $global_scratch*. The variables can also be used in standard Linux 
commands within job scripts.
