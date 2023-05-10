Using Filestore on the HEC
==========================

Overview of filestore
---------------------

User accounts on the HEC have three high performance 
personal filestore areas - **home**, **storage** and **scratch** - 
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

.. list-table:: 
   :header-rows: 1

   * - File Area	
     - Quota
     - Backup Policy
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
type: ``cd $global_scratch``. The variables can also be used in standard Linux 
commands within job scripts, though not in **#SBATCH** lines.

Viewing filestore quota and usage
---------------------------------

You can view your home, storage and 
scratch usage at any time using the command 
``gpfsquota``, which presents output from the 
``mmlsquota`` command in a more readable format. 
Typical output from gpfsquota looks like this:

.. code-block:: console

  Filesystem          Quota      Used     Avail     Use%     # files 
  home                  10G     5.50G     4.50G    55.00      24840 
  storage              100G    44.00G    56.00G    44.00      80640 
  scratch            10240G  2001.00G  8239.00G    19.54         23

For each filestore area the output shows the following info: 
your quota limit; how much you've used; how much is available; 
a percentage usage; and the total number of files you have in that area.

Best practice using the Filestore
---------------------------------

The main HEC filestores are shared across the login node 
and all compute nodes. With several thousand user jobs 
accessing the filestore simultaneously, we offer some 
best practice guidelines to avoid overloading the filesystem. 
When running many jobs at once, particular care should be 
taken to follow the guidelines below:

**Avoid making multiple jobs write to same the file.**
Multiple processes or jobs writing to the same file at the 
same time in an uncoordinated manner will result in missing 
or corrupted data. It will also slow the filesystem down as 
the write requests to a single file are queued up to run.

**Avoid creating large directories**
Directories become increasingly slower to manage (e.g. to 
view, add or delete files) the larger they become. Avoid 
placing more than a few thousand files in the same directory. 
Large directories should be deleted after use rather than 
reused - the directory size on disk never shrinks, even when 
files within it are deleted, making it slower to use next time.

**Avoid excessive file copying within jobs.**
Copying data from one location on the filesystem to another is 
an expensive operation - consider ways to minimise it, 
especially when running hundreds of jobs at the same time. For 
applications which need a local copy of an input file, consider 
using the ``ln -s`` command to create a soft link (short cut) 
rather than a full copy of the file. If output files need to 
be placed elsewhere on the filesystem, consider using the **mv** 
(file move) command.

**Avoid directory recursion/file searching within jobs.**
Having multiple jobs search for specific files through a large 
directory hierarchy can place a high load on the filesystem. 
Avoid use of recursive commands such as **find** within jobs.

**Use the temp directory for temporary files.**
The temp directory (referenced by the *$TMPDIR* variable) 
is located on the compute node running your job, and offers 
much higher file I/O and bandwidth and lower latency than 
network based storage. Wherever possible create any 
temporary/transient files in temp.

**Clean up your filestore regularly.**
With the ability to run hundred of jobs simultaneously, user 
filestore can quickly become cluttered with files (e.g. job 
stdout and stderr files). Small files in particular have a 
relatively high space overhead - even a small file will con
sume 32k of quota due to metadata and RAID overheads. An area 
filled with large numbers of small files can exhaust quota 
more quickly than expected.

**Offload valuable data to a permanent location.**
The HEC filestore is intended to enable high performance 
access to files for job runs, and should not be used for 
long term archiving of research data. Important research 
results should be offloaded from the HEC as soon as 
convenient to a more suitable location, such as LUNA. 
Please contact your local departmental IT liaison to discuss 
options for long-term research data archiving.

Transferring files to and from the HEC
--------------------------------------

Transferring files to the HEC from the IUS or a Linux or Mac desktop
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Files may be copied from your local machine (e.g. Linux desktop or the 
IUS) into your HEC filestore by using the command ``scp`` on your 
desktop/the IUS in the format below:
 
.. code-block:: console

  scp myfilename username@wayland.hec.lancaster.ac.uk:mydirectory

This will copy *myfilename* in the current directory into the directory 
*mydirectory* on the HEC.

Files can also be copied from the HEC onto your local machine by running 
this command on your local machine:
 
.. code-block:: console

  scp username@wayland.hec.lancaster.ac.uk:myfilename mydirectory

This will copy *myfilename* from the HEC into *mydirectory* on your local machine.

The first time you use ``scp``, it may prompt you with the following:
 
.. code-block:: console

  Host key not found from the list of known hosts.
  Are you sure you want to continue connecting (yes/no)? yes

Reply with yes and you will then receive a prompt for your password. With 
subsequent logins you will only have to give your password.

To copy directories and their contents, use ``scp -r``, for example :
 
.. code-block:: console

  scp -r mydirectory username@wayland.hec.lancaster.ac.uk:mydirectory

The scp command defaults to your home directory on the HEC, so relative 
file paths should start from there. For storage, scratch or other 
filestore areas using the full directory path is recommended. If 
you have difficulty determining the full path to a directory a useful approach
is to login to the HEC in a separate window, use the ``cd`` command 
to change to the desired directory and then run the command ``pwd`` (print 
working directory) which will show the full path to the directory. This can
then be copy-and-pasted into your desktop/IUS window containing the ``scp``
command.

.. note:
  While logged in to the HEC you can use the convenient shortcuts 
  *$global_storage* and *$global_scratch* to refer to your HEC storage and 
  scratch filestore areas, but these will only work for commands run on the HEC.

Transferring files to the HEC from a Windows PC
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Transferring files between the HEC and a local desktop PC requires your 
PC to run a client application capable of the *secure file transfer protocol*. 
There are several applications which support this. One such application is 
*WinSCP*, which can be found on `AppsAnywhere<https://apps.lancaster.ac.uk/>`_.

.. tip:
  Take care to ensure that text files such as job scripts are transferred in *text 
  mode* in order to avoid problems caused by format differences between Unix 
  and Windows text files. Binary files (i.e. files whose contents are not 
  solely ASCII text) should be transferred in *binary mode*.

Transferring files to the HEC from luna or other smb-compliant services
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The HEC login node has a high bandwidth connection to luna, the ISS-managed 
research data archive. While users can transfer files between folders on 
luna and the HEC using their PC, for large file transfers using the 
``smbclient`` tool to connect from the HEC directly to luna is *much* faster.

To connect to a folder on luna, ``smbclient`` needs two pieces of information:

**Faculty code**: Faculty codes are typically the faculty's acronym 
(e.g. FST for Faculty of Science and Technology, FASS for Faculty 
of Applied and Social Sciences) and are viewable from the top level 
of the *\\\\luna* tree under the Windows File Explorer.

**The folder path**: The path is the series of subfolders required 
to navigate the tree to the required subdirectory.

The command to connect to a folder on luna is then:

.. code-block:: console

  smbclient -D folder-path //luna/faculty-code

Here's an example for accessing the *py/gondor* folder - a restricted-access 
folder for a research group in the Faculty of Science and Technology:

.. code-block:: console

  wayland% smbclient -D py/gondor //luna/fst
  Enter LANCS\pacey's password: 
  Try "help" to get a list of possible commands.
  smb: \py\gondor\> 

You will be prompted to enter your password to authenticate your acecss to the 
requested folder. The *smb:* prompt will then allow you to use ftp-like commands 
such as **put** and **get** to transfer files and **cd** to navigate between
subfolders.  You can use the **help** command from the smb prompt to view a
list of possible commands. 

Here's an example of uploading a file to the folder:

.. code-block:: console
  smb: \py\gondor\> put myfile 

  putting file myfile as \py\gondor\myfile (529105.6 kb/s) (average 529105.6 kb/s)

The transfer speed here is notably higher than via file transfer on Windows, 
as the data is passed directly from the HEC to the folder on luna without having to pass 
through the user's PC first.

Using Kerberos tickets with smbclient
#####################################

If you are using ``smbclient`` several times during a session, you may find it useful
to use a kerberos ticket to remove the need to repeatedly enter a password for
authentication. Logging in to the HEC will automatically generate a kerberos ticket for 
you which is valid for 24 hours. To use the kerberos ticket, simply add the ``-k`` 
argument to the smbclient command:

.. code-block:: console

  smbclient -k -D folder-path //luna/faculty-code

If your ticket has expired, you can generate a new one by running the ``kinit`` 
command and entering your regular password.

For more advanced usage of smblcient, use the command ``man smbclient`` to view the 
command's manual page.
