The Open Cognitive Environment (Open-CE) Software Suite
=======================================================

Open-CE (the Open Cognitive Environment) is a freely available software stack 
which provides several GPU-enabled python Machine Learning libraries 
including TensorFlow, Keras, Torch in Torchvision. On the HEC, the
software stack is available as a virtual environment within 
a miniconda module, both of which will be set up upen calling the opence
module.

The following example job script shows how to invoke 
the virtual environment and run python code within it:

.. code-block:: bash

  #!/bin/bash
  #SBATCH -p gpu-short
  #SBATCH --gres=gpu:1
  #SBATCH --mem=10G
  #SBATCH --time=00:10:00
  #SBATCH --cpus-per-task=1

  source /etc/profile
  module add opence/1.10.0

  python mycode.py

