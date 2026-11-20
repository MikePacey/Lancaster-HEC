.. _small files:

A Note on Small Data Files
==========================

Many research datasets are distributed as sets of small files - often
numbering tens or hundreds of thousands of small files. For a number of reasons,
processing lots of small files on an HPC system is very inefficient:

- Parallel file systems are optimised to handle large file sizes not small sizes
- File I/O overheads are much greater on any network file system compared to local disk
- Meta data and other overheads are disproportionately large for small files
  resulting in user disk quota being exhausted quickly
- Many filesystems support only a finite number of inodes, which in turn means
  they support a finite (though still large) number of files

While some software applications will compensate for some of these inefficiences using various
strategies, if you aim to use the same data set for several jobs we recommend converting it
into the *squashfs* format. This converts sets of files into a single file which acts as a portable, 
read-only filesystem that can be mounted by local users. Testing on the HEC shows that processing of small files
can see a 5x-20x speedup when using the squashfs format.

Creating a squashfile
---------------------

Creating a squashfile from an existing directory
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have a data set uploaded onto the HEC within a directory, you can convert
it the whole directory structure into squashfs format with the `mksquashfs` command:

.. code-block:: console

   mksquashfs SOURCE/ MYSQUASHFILE   -noI -noD -no-xattrs

Where SOURCE is the directory containing your data set, and MYSQUASHFILE is the name
you want to call the squashed filesystem file (e.g. mydata.sqfs)

Creating a squashfile from a tarfile
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have a tarfile containing your data set you can convert it into squashfs format
without having to unpack it by using the `tar2sqfs` command:

.. code-block:: console

   tar2sqfs -x MYSQUASHFILE < MYTARFILE

Where MYSQUASHFILE is the name you want to call the squashed filesystem file (e.g. mydata.sqfs)
and MYTARFILE is the name of your tar file

Creating a squashfile from a zip file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can create a squashfile from an existing zip file without having to unpack it using
the following steps:

1) Creating a temporary directory to mount the zip file:

.. code-block:: console

   mkdir MYTEMPDIR

2) Mount the zipfile as a filesystem:

.. code-block:: console

   fuse-zip -r MYDATA.zip MYTEMPDIR

3) Convert the mounted file system into squashfs format:

.. code-block:: console

   mksquashfs MYTEMPDIR MYSQUASHFILE -noI -noD -no-xattrs

4) Cleanup the temporary directory/mount point:

.. code-block:: console

   fusermount -u MYTEMPDIR
   rmdir MYTEMPDIR

.. note::

An obvious question to ask would be: If we can already mount a zip file
as a file system, why convert it to squashfs? The answer is efficiency -
squashfs is designed to act like a filesystem, rather than linear archive
such as zip or tar

Using your squashfs file:
------------------------

Once you've created your squashfile you can mount it in a temporary directory
to access its contents:

.. code-block:: console

   mkdir MYTEMPDIR
   squashfuse MYSQUASHFILE MYTEMPDIR

To tidy up once you've finished:

.. code-block:: console

   fusermount -u MYTEMPDIR 

.. note::

Mounting a filesystem on a server only makes it accessible from that server. So, if
you mount it on the login node, it won't be visible to your jobs as they run on different
servers. So the squashfs filesystem will need to be mounted for each job

.. Tip:

Make sure each job has a unique temprary directory for its squashfs mountpoint to prevent
multiple jobs running on the same compute node from interferring with each other.
Here's an example script that creates the mountpoint in a job's unique temporary
directory.

.. code-block:: sh

  #!/bin/bash

  #SBATCH -p serial
  #SBATCH -J testjob

  source /etc/profile

  UNINQUEDIR=${TMPDIR}/MYTEMPMOUNT

  mkdir ${UNINQUEDIR}
  squashfuse MYSQUASHFILE ${UNINQUEDIR}

  echo MYSQUASHFILE is now available as ${UNINQUEDIR} for this job

  # <-- Main part of your job script goes here -->

  # Cleanup

  fusermount -u ${UNINQUEDIR}
  rmdir ${UNINQUEDIR}

Example Test Data
-----------------

If you want to practice with some pre-prepared data, the directory /data/hpc/aclImdb contains copies of the 
publically available `Large Movie Review Dataset <https://ai.stanford.edu/~amaas/data/sentiment/>`_ in tar, zip 
and squashfs formats. The data set contains around 100,000 small files.

Acknowledgement and references
------------------------------

Thanks to Richard Gilham and the Isambard-AI team for the idea

See also `Creating SquashFS Images to Support HPC and AI Datasets <https://arxiv.org/pdf/2610.20578>`_

