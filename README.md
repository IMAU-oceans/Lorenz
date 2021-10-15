# Compute Cluster Lorenz

This page contains basic information about our local compute cluster lorenz.

lorenz has the following setup:
* 1 main-node with 24 cores
  * with a total storage capacity of 220 TB
* 6 compute-nodes with in total 192 cores (32 cores/node)
  * 192 GB memory for each node 
  * 2TB local storage for each node
  
The cluster has Intel cores with a clockspeed of 2.9 GHz connected with \
a very fast infiniband connection (10Gb/s).
The batch system is SLURM.


## Get access

Request a lorenz account by writing an email to m.kliphuis@uu.nl      
         \
Please mention your solisid (if you have one)

## Login

When working on the Utrecht University network login with:

ssh -X yoursolisid@lorenz.science.uu.nl 

    e.g. ssh -X 1463124@lorenz.science.uu.nl 
    or   ssh -X vries001@lorenz.science.uu.nl
         
After that fill in your (Solis ID) password
         
The -X option allows a user to start graphical applications.
Sometimes -X does not work, then try -Y
  
When working from home first login to gemini.science.uu.nl with:

ssh -X yoursolisid@gemini.science.uu.nl

and log in to lorenz from there.
  
For more information about access to lorenz using Jupyter lab from home click [here](https://github.com/OceanParcels/UtrechtTeam/wiki/How-to-run-parcels-on-lorenz,-gemini-and-cartesius#remote-access-to-lorenz-using-jupyter-lab-from-home)

## Usage

The system works with modules. To check which modules are available type:

    module avail

To load for instance the latest Fortran, C and C++ compilers type:

    module load compiler

When you want to run programs with MPI then make sure to

    module load mpi/mpich-x86_64 

To check which modules are currently loaded type:

    module list


After logging in, the user is on the main node. This node is meant only for editing, compilation, submission of batch jobs, running short programs and viewing results. This is called *interactive usage*. Please do not run programs and scripts there that take long (> 30 min) or use a lot of memory. 
Also do not run many programs at the same time. These processes slow down the node (for all users) and should be done on the compute nodes. This is done by submitting a job script to the so called batch system. On lorenz this is SLURM and what it does is schedule your job i.e. decide when your job will be 
started on the compute nodes.

## Batch usage

With this we mean running a job on the compute nodes on usually more than 1 cores (parallel).  
The six compute nodes with each 32 cores are divided in the following so called partitions:

| partition      | max walltime (d-hh:mm:ss) | #nodes     | Max #nodes/job | Description                    |
| :------------: | :-----------------------: | :---------: | :-------------: | :----------------------------- |
| normal         | 5-00:00:00                |     4       |        2        | for production runs (default)  |
| short          | 01:00:00                  |     2       |        1        | for short runs                 |

When a user does not indicate the partition in the job script then by default the 'normal' partition nodes are used.
Jobs submitted to the 'short' partition have a higher priority. This means that when it is busy
on the system then your 'short' job will start sooner than your 'normal' one. Another rule is that the maximum number of 
jobs that each user can submit is 2. 

With the following command you can check which nodes are idle/free 

    sinfo | grep idle 

Here's the procedure of starting a job on the compute nodes:

Suppose you want to run an ocean model called HIM on 32 cores (1 compute node) and you think the job will take
at most 1 hour to complete. Say that you compiled the HIM source code into an MPI executable  ./HIM in the directory /nethome/my/run/directory/

In this directory then make a file:  submit.sh (or whatever you want to call it) with contents:

    #!/bin/bash -l
    #
    #SBATCH -J HIM              # the name of your job   
    #SBATCH -p short            # request the short partition, job takes less than 1 hour  
    #SBATCH -t 1:00:00          # time in hh:mm:ss you want to reserve for the job
    #SBATCH -n 32               # the number of cores you want to use for the job, SLURM automatically determines how many nodes are needed
    #SBATCH -o log_him.%j.o     # the name of the file where the standard output will be written to. %j will be the jobid determined by SLURM
    #SBATCH -e log_him.%j.e     # the name of the file where potential errors will be written to. %j will be the jobid determined by SLURM
 
    cd /nethome/my/run/directory/
    
    srun ./HIM < input          # the HIM executable needs an ascii file 'input' as argument


For an example that shows how to run a **python** job on the compute nodes click [here](python_example.md)

After that type on the commandline:

    sbatch submit.sh

to check if your job is running type:

    squeue

If you want to kill the job with e.g. jobid 123456 type:

    scancel 123456

to check more information about the job, for instance where it was submitted from (WorkDir) type:

    scontrol

followed by:

    show job 123456   
