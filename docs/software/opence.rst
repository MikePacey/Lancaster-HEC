The Open Cognitive Environment (Open-CE) Software Suite
=======================================================

Open-CE (the Open Cognitive Environment) is a freely available software stack 
which provides several GPU-capable Machine Learning libraries under the anaconda 
package, including TensorFlow, Keras, Caffe and Torch. On the HEC, the
software stack is available as the opence_env conda virtual environment within 
the module anaconda3-gpu. The following example job script shows how to invoke 
the virtual environment and run python code within it:

.. code-block:: bash

  #!/bin/bash
  #SBATCH -p gpu-short
  #SBATCH --gres=gpu:1
  #SBATCH --mem=10G
  #SBATCH --time=00:10:00
  #SBATCH --cpus-per-task=1

  source /etc/profile
  module add cuda
  module add anaconda3-gpu
  source activate opence_env

  python mycode.py

