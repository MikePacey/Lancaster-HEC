CP2K
====

CP2K is a quantum chemistry and solid state physics software 
package that can perform atomistic simulations of solid state, 
liquid, molecular, periodic, material, crystal, and biological systems.

Invoking CP2k
-------------

To access the default version of CP2K on the HEC, simply type the command:

.. code-block:: console

  module add cp2k

To see what other versions of CP2K are currently available, type:

.. code-block:: console

  module avail cp2k

For more details on the module command, see Using environment modules on the HEC.

A sample parallel batch job script for CP2K
-------------------------------------------

CP2K batch jobs may be run by creating a batch job control script 
(for example, called **CP2K_job.com**) like the following, which 
will submit the H2O-32 benchmark model to run on two 40-core compute nodes:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p parallel
  #SBATCH --nodes=2
  #SBATCH -C node_type=40core192G
  #SBATCH --exclusive

  source /etc/profile
  module add cp2k/6.1.0

  ulimit -s unlimited

  mpirun cp2k.popt H2O-64.inp

Further Reading
---------------

Further information can be found on the following pages:

* The CP2K `Home Page <https://www.cp2k.org/>`_

* The CP2K `Online Manual <https://manual.cp2k.org/#gsc.tab=0>`_
