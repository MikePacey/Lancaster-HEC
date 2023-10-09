Matlab
======

To use MATLAB, the MATLAB compiler, or standalone executables 
created with the MATLAB compiler, you must first load the matlab 
module via the following command:

.. code-block:: console

  module add matlab

Using the module's primary name on its own will result in the 
defalt version of matlab being used. To see what other versions of 
matlab are currently available, type:

.. code-block:: console

  module avail matlab

See the :ref:`Using environment modules on the HEC <envmods>` page for more details on the module command.

A sample matlab and batch job script
------------------------------------

Matlab code can be executed by calling its main function and optional arguments
from within a call to matlab. Consider the following simple function which returns
twice the value it receives as an argument, contained in file called *twotimes.m*:

.. code-block:: bash

  function twotimes (x)

  2 * x

This function can be called with a job script like this:

.. code-block:: bash

  #!/bin/bash

  #SBATCH -p serial
  #SBATCH --mem=1G

  source /etc/profile
  module add matlab/2022a

  matlab -singleCompThread -nodisplay -nosplash -r "twotimes (3.1)"

Further Reading
---------------

The Matlab `Home Page <https://uk.mathworks.com/products/matlab.html>`_
