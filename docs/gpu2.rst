GPU Jobs
========

GPUs (Graphics Processing Units) are a specialist type of computing hardware originally designed for displaying high performance graphics and now increasingly used to perform specialist computing tasks in the domain of research computing. The HEC offers a small number of GPU compute nodes contributed by University research groups. This page describes how to submit jobs running GPU-capable software on the HEC.

.. note:
  Most applications aren't GPU-capable as software must be written specifically to make 
  use of GPU hardware. Check the User Guide for your application to see if it supports
  GPU use, and whether it supports multiple GPUs. When testing a new GPU application be
  sure to use the GPU Resource Monitoring advice below to ensure that your application
  is genuinely using a GPU.

Understanding GPU types
-----------------------

The HEC currently offers four different itypes of GPU of differing ages, and supporting
different workloads. The different GPUs support different types of floating point arithmetic
to different degrees; the older V100 GPUs offer good support for double and single precision (FP64 and FP32),
the L40 and L40S offer better performance for single precision and lower (ie. FP32, FP16 and below) -
typically used in Neural Net / LLM workloads - while the small number of H200 GPUs are powerful
all-rounders. The table below summarizes basic stats for each type:

.. list-table::
  :header-rows: 1

  * - GPU type
    - Achitecture
    - GPU memory
    - Best used for workloads needing
  * - V100
    - Volta
    - 32 GB
    - double and single precision (FP64 and FP32)
  * - H200
    - Hopper
    - 141 GB
    - any precision type, very large models
  * - L40
    - Lovelace
    - 48GB
    - single precision (LP32) or lower
  * - L40S
    - Lovelace
    - 48GB
    - single precision (LP32) or lower

GPU queues
----------

While most GPU jobs will be short, the HEC GPU nodes offer support for a small number
of longer-running jobs. The GPU nodes are also divided up into separate queues depending on their workloads focus (e.g. strong support for FP64, or strong support of FP32 and below).

The V100 and H200 GPUs are available on three queues: **gpu-short**,
**gpu-medium** and **gpu-long** while the L40 and L40S GPUs are available on the **astro**
queue. Each queue has different job run-time and per-user GPU limits:

.. list-table::
  :header-rows: 1

  * - Queue name
    - Time limit
    - GPU types
    - Max # of GPUs per user
  * - gpu-short
    - 12 hours
    - V100 and H200
    - Unlimited
  * - gpu-medium
    - 48 hours
    - V100 and H200
    - 6
  * - gpu-long
    - 7 days
    - V100 and H200
    - 2
  * - astro
    - 24 hours
    - L40 and L40S
    - 15

.. note::

   GPU jobs have a maximum runtime, dependent on the queue they are submitted to,
   as described in the table above.
   It is *strongly* recommend to use an accurate **--time=** resource request as
   this helps with scheduling jobs on the small number of GPUs available. Longer
   jobs will stay pending for longer, as it's more difficult for the job scheduler
   to fairly schedule longer running jobs.

**Job Priority**: Users who are members of the Observational Astrophysics Group will have access
to the additional *astro-tier1* and *astro-tier2* groups which give jobs priority access to
the L40 GPU nodes they have contributed.  Priority users will be notified
on account creation that they have access to those priority
queues. All other users will have access to the *astro* queue which gives access to the L40
and L40S GPU nodes which uses the same job FairShare system as the rest of the HEC.


Example of a batch GPU job script
---------------------------------

The following job will run the CUDA **nbody** demo application as a benchmark on a GPU:

.. code-block:: bash

  #!/bin/bash
  #SBATCH -p gpu-short
  #SBATCH --gres=gpu:1
  #SBATCH --mem=10G
  #SBATCH --time=00:10:00
  #SBATCH --cpus-per-task=1

  source /etc/profile
  module add cuda/12.0

  nbody -benchmark -numbodies=2500000 -numdevices=1

.. note::

  Not all applications will automatically detect how many GPUs should be used.
  Specifying whether to use GPUs - and how many to use - varies widely
  between applications. In the above example the command line argument *-numdevices=*
  needs to be added to the **nbody** demo application. Refer to your application's User 
  Guide for details on the correct syntax to use.

The main differences with GPU-based jobs are: 

* the use of a special queues (**gpu-short** in the above example)
* the use of a resource request for GPUs using the **--gres=gpus:** syntax
* the use of a job time limit using the **--time=** resource request, with the value in the format HH:MM:SS

As GPU jobs will also require a certain amount of CPU resource and the compute node's main memory,
these values also need to specified in a job request. Be aware that the compute node's main memory
is separate to the GPU's own memory and so needs to be requested in each job. Jobs don't
need to request GPU memory - as each job requests dedicated GPUs, the whole of each requested
GPU's memory is made available to the job automatically.

.. note::

  Take care when requesting CPU and (main) memory resource in GPU jobs, as claiming too much of either
  will result in little (or no) resource for other GPU jobs on the same node. If a job's performance
  is significantly increased by large amounts of CPU resource, it's likely that the application is not
  making good use of the GPU and would best run as a CPU-only job on the serial or parallel queues.

Multi-node GPU jobs
-------------------

Jobs which scale well to large numbers of GPUs can request multiple GPU nodes for each job.
The following example requests all (3) GPUs on 2 nodes:

.. code-block:: bash

  #!/bin/bash
  #SBATCH -p gpu-short
  #SBATCH --nodes=2
  #SBATCH --exclusive
  #SBATCH --gres=gpu:3
  #SBATCH --mem=30G
  #SBATCH --cpus-per-task=3

  # Job command here

Note that such jobs will need some mechanism to launch across multiple nodes, such as MPI's
``mpirun``. Refer to the User Guide for your application to learn if and how multi-node / distributed
computing GPU workloads are supported.

Requesting specific GPU types
-----------------------------

With different cards within the same queue offering different capabilities, users can opt to request
a specific gpu type. This can be done by modifying the ``--gres=gpu`` resource request to include
the desired GPU type. The following table lists how to request a single GPU of each GPU type:

.. list-table::
  :header-rows: 1

  * - GPU type
    - Available on queues
    - GRES line to use in a job scripts
  * - V100
    - gpu-short, gpu-medium, gpu-long
    - --gres=gpu:tesla_v100-pcie-32gb:1
  * - H200
    - gpu-short, gpu-medium, gpu-long
    - --gres=gpu:nvidia_h200_nvl:1
  * - L40
    - astro
    - --gres=gpu:nvidia_l40:1
  * - L40S
    - astro
    - --gres=gpu:nvidia_l40s:1

Here's an exmaple, using the original nbody GPU job script fropm above, modified to request
and L40S GPU on the astro queue:

.. code-block:: bash

  #!/bin/bash
  #SBATCH -p gpu-short
  #SBATCH --gres=gpu:nvidia_l40s:1
  #SBATCH --mem=10G
  #SBATCH --time=00:10:00
  #SBATCH --cpus-per-task=1

  source /etc/profile
  module add cuda/13.2

  nbody -benchmark -numbodies=2500000 -numdevices=1

GPU resource monitoring
-----------------------

GPU jobs on the HEC are logged using NVidia's Data Centre GPU Manager
suite, which summarises how much GPU resource is used by each job on
a per-GPU level. The logging is intended to highlight cases
where jobs make very little - or no - use of GPU resource, which indicates
that they would be better run as CPU-only jobs on the serial or parallel
queues. The usage data is appended
to the end of each jobs's *stdout* file. An example for the **nbody** example job above 
would be:

.. code-block:: bash

  Successfully retrieved statistics for job: testuser-gpu06-2265_. 
  +------------------------------------------------------------------------------+
  | GPU ID: 0                                                                    |
  +====================================+=========================================+
  |-----  Execution Stats  ------------+-----------------------------------------|
  | Start Time                         | Fri Jul  7 13:47:43 2023                |
  | End Time                           | Fri Jul  7 13:50:26 2023                |
  | Total Execution Time (sec)         | 162.76                                  |
  | No. of Processes                   | 1                                       |
  +-----  Performance Stats  ----------+-----------------------------------------+
  | Energy Consumed (Joules)           | 25904                                   |
  | Power Usage (Watts)                | Avg: 220.23, Max: 224.652, Min: 214.315 |
  | Max GPU Memory Used (bytes)        | 694157312                               |
  | SM Clock (MHz)                     | Avg: 1380, Max: 1380, Min: 1380         |
  | Memory Clock (MHz)                 | Avg: 877, Max: 877, Min: 877            |
  | SM Utilization (%)                 | Avg: 100, Max: 100, Min: 100            |
  | Memory Utilization (%)             | Avg: 0, Max: 0, Min: 0                  |
  | PCIe Rx Bandwidth (megabytes)      | Avg: N/A, Max: N/A, Min: N/A            |
  | PCIe Tx Bandwidth (megabytes)      | Avg: N/A, Max: N/A, Min: N/A            |
  +-----  Event Stats  ----------------+-----------------------------------------+
  | Single Bit ECC Errors              | 0                                       |
  | Double Bit ECC Errors              | 0                                       |
  | PCIe Replay Warnings               | 0                                       |
  | Critical XID Errors                | 0                                       |
  +-----  Slowdown Stats  -------------+-----------------------------------------+
  | Due to - Power (%)                 | 0                                       |
  |        - Thermal (%)               | 0                                       |
  |        - Reliability (%)           | Not Supported                           |
  |        - Board Limit (%)           | Not Supported                           |
  |        - Low Utilization (%)       | Not Supported                           |
  |        - Sync Boost (%)            | 0                                       |
  +--  Compute Process Utilization  ---+-----------------------------------------+
  | PID                                | 1080582                                 |
  |     Avg SM Utilization (%)         | 99                                      |
  |     Avg Memory Utilization (%)     | 0                                       |
  +-----  Overall Health  -------------+-----------------------------------------+
  | Overall Health                     | Healthy                                 |
  +------------------------------------+-----------------------------------------+

The most relevant entry is the "SM Utilization (%)" line, which shows the average,
minimum and maximum utilisation of GPU cores. The line above that labelled "Max GPU Memory 
Used (bytes)" reports the maximum amount of GPU memory (not to be confused with the compute
node's main memory) used by the job.

Each jobs' GPU utilisation can also be monitored while the job is running using the **qgputop** 
command. The command accepts the flags ``-u username``  and will list the GPU usage
of the jobs by the specified user.

.. code-block:: bash

  Node gpu06
  Mon Jul 10 11:53:39 2023       
  +-----------------------------------------------------------------------------+
  | NVIDIA-SMI 525.85.12    Driver Version: 525.85.12    CUDA Version: 12.0     |
  |-------------------------------+----------------------+----------------------+
  | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
  | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
  |                               |                      |               MIG M. |
  |===============================+======================+======================|
  |   0  Tesla V100-PCIE...  On   | 00000000:3B:00.0 Off |                    0 |
  | N/A   52C    P0   215W / 250W |   1008MiB / 32768MiB |    100%      Default |
  |                               |                      |                  N/A |
  +-------------------------------+----------------------+----------------------+
                                                                               
  +-----------------------------------------------------------------------------+
  | Processes:                                                                  |
  |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
  |        ID   ID                                                   Usage      |
  |=============================================================================|
  |    0   N/A  N/A   1230218      C   nbody                            1004MiB |
  +-----------------------------------------------------------------------------+

The above example shows output from a job running the CUDA **nbody** demo. The first box
in the output shows GPU utilisation in the field "GPU-Util" (the 100% value in the
output). The second box shows what processes are currently attached to a GPU and
how much GPU memory they are consuming.

.. note::

  If you have multiple jobs running on a gpu node, this tool will not differentiate
  which GPU belongs to which job.

.. note::

  CPU and main system memory usage for GPU jobs can be monitored using the ``qtop``
  command in the same manner as CPU-only jobs

Compiling CUDA-capable code
---------------------------

NVidia's CUDA library (available on the HEC as the cuda module) provides the ``nvcc`` 
compiler for compiling GPU-capable code written in C or C++.

After adding the cuda environment, the compiler can invoked using arguments common to most compilers.

For instance, in the Vector Addition example from this `Oak Ridge Leadership Computing Facility 
tutorial <https://www.olcf.ornl.gov/tutorials/cuda-vector-addition/>`_ the source file vecAdd.cu 
can be compiled into an executable named vector_add with the command:

``nvcc vecAdd.cu -o vector_add``

To run this application, the call to vector_add can be included within a standard 
GPU-capable job script:

.. code-block:: bash

  #!/bin/bash
  #SBATCH -p gpu-short
  #SBATCH --gres=gpu:1
  #SBATCH --mem=1G
  #SBATCH --time=00:10:00
  #SBATCH --cpus-per-task=1

  source /etc/profile
  module add cuda

  ./vector_add

A note on CUDA versions
-----------------------

The major version number for CUDA broadly correspond to the maximum compute capability level of GPU
they can support. For the L40, L40S and H200 GPU nodes, these all support CUDA applications up to version 13. 
The older V100 GPU nodes support CUDA applications up to version 12.9 - the V100 GPUs cannot support CUDA 13
features, so check what level of CUDA your application supports.

Further Reading
---------------

* The `CUDA main page <https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html>`_

* NVidia's `documentation for the nvcc compiler <https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html>`_

GPU-enabled Machine Learning Libraries
--------------------------------------

The popularity of Machine Learning-based applications has created something of a Wild West of libraries
supporting them. Applications which require python libraries such as Tensorflow, Keras and Torch usually need
a wide array of supporting libraries, often with very specific version requirements. As it's not feasible
to support the combinatorial explosition of library combinations centrally, users are advised to create
their own software stacks specifically for their own software stacks using tools such as miniforge, conda and mamba.
Please see :doc:`/software/python` for an brief explanation of how best to use these tools on the HEC.

GPU Hardware Contributions
--------------------------

The HEC currently offers the following GPU nodes:

.. list-table::
  :header-rows: 1

  * - GPU type
    - # GPUs
    - # CPU Cores
    - Main memory
    - # GPU nodes
    - Contributor
  * - NVidia V100 32GB
    - 3
    - 32
    - 192G
    - 8
    - HEP Research Group (1), CHICAS Research Group (1), Maths and Stats Dept (6)
  * - NVidia H200 141GB
    - 2
    - 48
    - 512G
    - 2
    - Maths and Stats Dept
  * - NVidia L40 48G
    - 4
    - 32
    - 512G
    - 2
    - Observational Astrophysics Group
  * - Nvidia L40S 48G
    - 3
    - 32
    - 512G
    - 5
    - Central University funding
