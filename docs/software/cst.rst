CST
===

CST is electromagnetic field simulation software, with 
a license provided by the Engineering department. The 
application can be run in parallel both on CPUs and GPUs.

In both examples below, the CST input file is called 
**my_data.cst** and uses the Microwave Studio with the 
time domain solver.

A sample CPU parallel job script for CST
----------------------------------------

The following is a sample CPU parallel job script for 
CST which uses all cores on 40-core compute node:

.. code-block:: bash

  #bin/bash

  #SBATCH --part=parallel
  #SBATCH --nodes=1
  #SBATCH -C node_type=10Geth64G
  #SBATCH --exclusive

  source /etc/profile
  module add CST/2022

  export OMP_NUM_THREADS=$NSLOTS

  cst_design_environment -m -r --numthreads=$NSLOTS mymodel.cst

A sample GPU job script for CST
-------------------------------

Further Reading
---------------

Further information can be found on the following pages:

* CST `main web page <https://www.cst.com/>`_

* CST `command line options <https://space.mit.edu/RADIO/CST_online/advanced/commandlineoptions.htm>`_
