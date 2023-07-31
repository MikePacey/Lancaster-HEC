Requesting specific node types
==============================

While the HEC's compute nodes are all similar in 
their underlying architecture, different eras of compute 
nodes are of very different sizes. For the purposes of 
parallel jobs which request multiple nodes, 
it's important to ensure that each node used is of broadly 
the same type. There may also be some circumstances where 
a particular application may perform better on one type of 
node than another. Jobs can request that they be restricted 
to a specific type of node using the job resource request 
*node_type* by using the following syntax in a job script:

.. code-block:: bash

  #SBATCH -C node_type=type

Where *type* is one of the following:


.. list-table::
  :header-rows: 1

  * - Type
    - # cores
    - Processor
    - Memory
    - Interconnect
  * - 10Geth64G
    - 16
    - Intel Ivy Bridge, Haswell or Broadwell
    - 64G
    - 10Gb low latency
  * - 10GethAny
    - 16
    - Intel Ivy Bridge, Haswell or Broadwell
    - 64G
    - 10Gb low latency
  * - 40core192G
    - 40
    - Intel Skylake or Cascade Lake
    - 192G
    - 25Gb low latency
  * - 64core256G
    - 64
    - Intel Ice Lake
    - 256G
    - 25Gb low latency

.. note::

  The 10GethAny node type exists only to provide backwards compatibilty - it
  used to refer to both the existing 10Geth64G and the now retired 10Geth128G nodes.

The scheduler will handle *node_type* requests in an intelligent way, 
depending on job type:

For serial jobs:

* If no node_type is specified, the job is free to launch on any type of compute node. The node_type should typically not be used for serial jobs.

For parallel jobs making an exclusive resource request (ie, requesting one or more whole compute nodes):

* If no node_type is specified, the job will be run as though **node_type=10Geth64G** had been specified.

* If **node_type=10Geth64G** or **node_type=10GethAny** is specified, and the memory resource request will be set to 3.95G per core (for a total of 63.2G per node).

* If **node_type=40core192G** is specified, the job will be eligible to run on compute nodes of type 40core192G with a memory resource request of 4.75G per core (for a total of 190G per node).

* If **node_type=64core256G** is specified, the job will be eligible to run on compute nodes of type 64core256G with a memory resource request of 3.95G per core (for a total of 252.8G per node).

For parallel jobs that do not make an exclusive request (ie, OpenMP jobs requesting a number of cores all on the same compute node):

* If no node_type is specified the job will be eligible to run on any node that can suport the number of cores and amount of memory requested
