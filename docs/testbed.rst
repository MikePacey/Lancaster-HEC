HEC 3.0 Testbed - check here for the current status
==================================================

This page contains a summary of the current testbed setup. 
Note: this page assumes you've read and understood the rest 
of the HEC 3.0 User Guide below.

The HEC testbed is currently configured with a small number of
compute nodes assigned to the queues **serial**, **parallel** and **test**
with similar functionality to the current production HEC
environment. The aim of this setup is allow users to both
become familiar with the new SLURM job scheduler and to test
compatibility of existing applications on the new operating
system, Rocky 8 (a downstream copy of RedHat Enterprise Linux 8)

The **test** queue has a maximum runtime limit of 5 minutes. The **serial**
and **parallel** queues both currently have a maximum runtime limit
of 2 hours. This is to allow user testing without the testbed
becoming filled with long-running jobs.

Production queues
-----------------

The HEC 3.0 testbed also contains a number of compute nodes in queues
**prod-serial** and **prod-parallel**. These queues currently have no
runtime limits and so can be used for longer-running workloads.

.. warning::

  The HEC 3.0 testbed is still undergoing development, and so
  compute nodes may need to be taken down on short notice. Please
  do not submit workloads that require a guarantee of long uptimes.
  (Ie, jobs should either be for testing purposes, or production
  workloads that are cabapable of checkpointing).
