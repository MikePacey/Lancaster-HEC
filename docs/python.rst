Python, Conda and Mamba
=======================

.. Note::

   This guide contains instructions on a simple, clean way
   to get python with conda or mamba working on the HEC.
   More sophisticated approaches are available. If you're
   an experienced conda or mamba user, please read this
   guide first for an explanation of the workarounds needed.

Introduction
------------

The base python application is usually supplemented by python
libraries. With hundreds of libraries available and complex version
dependencies between those libraries, many users prefer to install
their own library sets.

Installing python libraries is made easier using tools such
as conda or mamba (and occasionally pip), which allow users to create *virtual
environments* into which they can install their preferred libraries.
As these tools are written mostly for desktop users, a little
finessing is required for them to work on the HEC and other HPC systems.

The four basic steps are:

1. Redirect the conda, mamba and pip hidden caches from home to your storage area to prevent running
   out of disk quota. (This step needs only to be performed once per user).

2. Add the miniforge module to your shell environment in order to access conda and/or mamba.

3. Create your virtual environment with the libries you need.

4. Activate your virtual enviroment.

Redirecting conda, mamba and pip's hidden caches
------------------------------------------------

Conda, mamba and pip each create hidden caching directories. These
default to the user's home directory, and as that is very small
on the HEC it's preferable to redirect them to the user's much larger
storage are.

The following commands will create the cache directories in storage and
create symlinks to redirect from the user home area:

.. code-block:: console

  mkdir $global_storage/.cache $global_storage/.conda
  ln -s $global_storage/.cache ~/.cache
  ln -s $global_storage/.conda ~/.conda

.. Note::

  The above commands assume starting with a clean slate. If you've
  previously used conda, mamba and/or pip some of these directories
  may already exist, which will result in error messages. The cleanest solution
  in this case is to delete those directories and rerun the above commands

.. Tip::

  The above commands only need to run once. Once the redirection is in place
  it will persist until you delete the directories and symlinks. Your storage
  area is still only a finite size, so you may want to occasionally clean up the contents
  of your .cache and .conda directories to claim back disk space!

Accessing conda or mamba on the HEC
------------------------------------

Both conda and mamba are available via the centrally installed
module *miniforge*, so you won't need to install it yourself.
To access it, use the command ``module add miniforge``.
Once the module has been added, you can use it to create new virtual
environments, access existing onesi, and install python libraries into them.

Creating and activating virtual environments
--------------------------------------------

By default, conda and mamba virtual environments will be created
in your home area, which has only a small disk quota. The simplest workaround is to 
create virtual environments in your storage area by specifying the full 
path to the environment. 

For example, to create a virtual environment called *myenv* using mamba:

``mamba create -p $global_storage/myenv``

You can specify a list of libraries you want your virtual enviromnent to contain by adding them
to the end of the above command, or you can install them once you've activated the environment.

Once the virtual environment is created, you can access it by ensuring that the miniforge
module is added and then running the command:

``source activate $global_storage/myenv``

Additional libraries can then be installed with the command ``mamba install packagename``

.. Note::

   The conda and mamba documentation advise using the commands ``conda activate`` or ``mamba activate``.
   On the HEC we recommend using ``source activate`` as the alternatives will edit your shell startup 
   file, which may interfere with the shell environment for your jobs.

.. Tip::

   Once you've created a virtual environment you can access it in your jobs by adding the
   ``module add`` and ``source activate`` commands described above.

GPU enabled python libraries
============================

Some python libraries offer GPU support, but it may be necessary to use different channels to
install these as the default conda-forge channel will often supply CPU-only versions.
As an example, here's a command to install GPU-enabled versions of the *torch* and *torchvision*
python libraries using specialist channels. The command can be run once your virtual
environment has been activated:

``mamba install pytorch torchvision pytorch-cuda -c pytorch -c nvidia``


Further Reading
===============

* `Managing Conda <https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html>`_ - note that mamba is a drop in replacement for conda, with compatable commands to conda
