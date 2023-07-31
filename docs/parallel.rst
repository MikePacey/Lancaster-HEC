Overview of Parallel jobs
=========================

The web page explaining basic job submission assumed that jobs
were serial (single core) and so were submitted to the serial
partition (queue). Many research applications are able to use
multiple cores in order to speed up their runs. Such jobs
are submitted to the parallel partition. The HEC supports both
shared-memory and distributed memory paraellism, as explained
in the sections below.

**Shared memory parallelism** refers to parallel applications which must
have all their components (e.g. process or threads) running
on the same compute node. OpenMP (the Open Multiprocessing standard)
is a common standard used to create multi-threaded applications,
which support shared memory parallelism.

**Distributed memory parallelism** refers to parallel applications which
can run across multiple compute nodes, and their components communicate
across the network. MPI (the Message Passing Interface) is a common
standard used to create multi-process applications, along with an application
launcher which will distribute the application's processes across compute
nodes.

.. note::

  Applications must be specifically written to run in parallel - either
  shared- or distrubted- memory. Submitting a non-parallel (ie, serial)
  job to the parallel queue won't result in any speed up - it will
  run in serial.

.. tip::

  More cores doesn't always guarantee an application will run faster:
  No parallel application scales up to larger numbers of cores perfectly, and
  the scalability varies both between applications and sometimes between
  different use cases or models within the same application. Most applications will
  run slower if made to run on too many cores, as the constant
  communications required between the different components of the application
  will eventually dominate over 'real' computational work.
  When trying out a new
  parallel application (or a new type of workload), it's best submit some test jobs of different
  sized to find out the sweet spot for scaling for your particular workload.
