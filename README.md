# Compute Cluster Lorenz

This page contains basic information about our local compute cluster Lorenz.

Lorenz has the following setup:
* 1 main-node with 24 cores
  * with a total storage capacity of 220 TB
* 6 compute-nodes with in total 192 cores (32 cores/node)
  * 192 GB memory for each node 
  * 2TB local storage for each node
  
The cluster has Intel cores with a clockspeed of 2.9 GHz connected with \
a very fast infiniband connection (10Gb/s).
The batch system is SLURM.


## Get access

Request a Lorenz account by writing an email to m.kliphuis@uu.nl      
         \
Please mention your solisid (if you have one)

## Login

When working on the Utrecht University network login with:

ssh -X &lt;Your Solis ID>@lorenz.science.uu.nl 

    e.g. ssh -X 1463124@lorenz.science.uu.nl 
    or   ssh -X vries001@lorenz.science.uu.nl
         
After that fill in your (Solis ID) password
         
The -X option allows a user to start graphical applications.
Sometimes -X does not work, then try -Y
  
When working from home first establish a VPN connection to the UU
  
## Usage

The system works with modules. To check which modules are available type:

    module avail

To load for instance the latest Fortran, C and C++ compilers type:

    module load compiler

When you want to run programs with MPI then make sure to

    module load mpi/mpich-x86_64 

To check which modules are currently loaded type:

    module list


After logging in, the user is on the main node. Please do not run programs and scripts there that take long (> 30 min) or use a lot of memory. 
Also do not use more than 2 cores here. These type of jobs slow down the node (for all users) and should be done on the compute nodes. This is done by submitting a job script to the so called batch system. On Lorenz this is SLURM and what it does is schedule your job i.e. decide when your job will be 
started on the compute nodes.

Here's the procedure of starting a job on the compute nodes:

Suppose you want to run an ocean model called HIM on 32 cores (1 compute node) and suppose \
that you compiled the HIM source code into an MPI executable  ./HIM in the directory /nethome/my/run/directory/

In this directory then make a file:  submit.sh (or whatever you want to call it) with contents:

    #!/bin/bash -l
    #
    #SBATCH -J HIM              # the name of your job   
    #SBATCH -t 10:00:00         # time in hh:mm:ss you want to reserve for the job
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
