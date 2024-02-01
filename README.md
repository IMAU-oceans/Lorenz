# Compute Cluster Lorenz

This page contains basic information about our local compute cluster lorenz.

lorenz has the following setup:
* 1 main-node with 24 cores
  * with a total storage capacity of 394 TB
* 9 compute-nodes with in total 288 cores (32 cores/node)
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
  
For more information about access to lorenz using Jupyter lab from home click [here](https://github.com/OceanParcels/UtrechtTeam/wiki/How-to-run-parcels-on-lorenz-or-gemini#jupyter-lab-on-a-compute-node)

## Transfer data to/from Lorenz

When working on the Utrecht University network, copy data to Lorenz with the command:

scp -r data_to_copy yoursolisid@lorenz.science.uu.nl:/target/path/on/lorenz/

    e.g.  scp -r test.nc vries001@lorenz.science.uu.nl:/nethome/vries001/
    
When you want it the other way around i.e. copy data from Lorenz to your local machine on the Utrecht University network then type:

scp -r yoursolisid@lorenz.science.uu.nl:/path/to/file/on/lorenz .

    e.g. scp -r vries001@lorenz.science.uu.nl:/nethome/vries001/test.nc .
    
After the scp command you will be asked to fill in your (Solis ID) password

When NOT working on the Utrecht University network (e.g. at home) then use the intermediary server Gemini and the 'ProxyJump' option to quickly transfer data. You can then copy data to Lorenz with the command:

scp  -o ProxyJump=yoursolisid@gemini.science.uu.nl data_to_copy yoursolisid@lorenz.science.uu.nl:/target/path/on/lorenz/

    e.g. scp  -o ProxyJump=vries001@gemini.science.uu.nl test.nc vries001@lorenz.science.uu.nl:/nethome/vries001/

When you want it the other way around i.e. copy data from Lorenz to your local machine (e.g. at home) then type:

scp  -o ProxyJump=yoursolisid@gemini.science.uu.nl yoursolisid@lorenz.science.uu.nl:/path/to/file/on/lorenz .

    e.g. scp  -o ProxyJump=vries001@gemini.science.uu.nl vries001@lorenz.science.uu.nl:/nethome/vries001/test.nc .
    
After the scp command you will be asked to fill in your (Solis ID) password twice, one time for Gemini and one for Lorenz.

## Usage

The system works with modules. To check which modules are available type:

    module avail

To load for instance the latest Fortran, C and C++ compilers type:

    module load compiler

When you want to run programs with MPI then make sure to

    module load mpi/mpich-x86_64 

To check which modules are currently loaded type:

    module list


After logging in, the user is on the main node. This node is meant only for editing, compilation, submission of batch jobs, running short programs and viewing results. This is called *interactive usage*. Please do not run programs and scripts there that take long (> 10 min) or use a lot of memory. 
Also do not run many programs at the same time. These processes slow down the node (for all users) and should be done on the compute nodes. This is done by submitting a job script to the so called batch system. On lorenz this is SLURM and what it does (among other things) is schedule your job i.e. decide when your job will be 
started on the compute nodes.

## Batch usage

With this we mean running a job on one or more cores of the compute nodes.  
The compute nodes with each 32 cores are divided in the following so called partitions:

| partition      | max walltime (d-hh:mm:ss) | #nodes      | Max #nodes/job  | Max #jobs/user | Description                          | 
| :------------: | :-----------------------: | :---------: | :-------------: | :------------- | :------------------------------------|
| normal         | 5-00:00:00                |     8       |        2        |        3       | for production runs (is the default) |   
| short          | 03:00:00                  |     1       |        1        |        2       | for short runs and testing/debugging |

When a user does not indicate the partition in the job script then by default, cores of the 'normal' partition nodes are used. 

It is advised to put jobs that take less than 3 hours in the 'short' partition for which we reserved one node (32 cores).
\
Jobs that take longer than 3 hours should be put in the 'normal' partition for which we reserved the remaining eight nodes (256 cores).

A user can reserve at most 2 nodes for a job in the 'normal' partition and at most 1 node for a job in the 'short’ partition. 
Each user can start at most 3 jobs in parallel in the 'normal' partition and at most 2 jobs in parallel in the 'short' partition.

With the following command you can check which nodes are idle/free:

    sinfo | grep idle 

Here's the procedure for starting a job on the compute nodes:

Suppose you want to run an ocean model called HIM on 32 cores (1 compute node) and you think the job will take
at most 3 hours to complete. Say that you compiled the HIM source code into an MPI executable  ./HIM in the directory /nethome/my/run/directory/

In this directory then make a file:  submit.sh (or whatever you want to call it) with contents:

    #!/bin/bash -l
    #
    #SBATCH -J HIM              # the name of your job   
    #SBATCH -p short            # request the short partition, job takes less than 3 hours  
    #SBATCH -t 3:00:00          # time in hh:mm:ss you want to reserve for the job
    #SBATCH -n 32               # the number of cores you want to use for the job, SLURM automatically determines how many nodes are needed
    #SBATCH -o log_him.%j.o     # the name of the file where the standard output will be written to. %j will be the jobid determined by SLURM
    #SBATCH -e log_him.%j.e     # the name of the file where potential errors will be written to. %j will be the jobid determined by SLURM
 
    cd /nethome/my/run/directory/
    
    srun ./HIM < input          # the HIM executable needs an ascii file 'input' as argument

Probably the easiest editor to use on Lorenz is **nano**. Also **vi** can be used but it has a steeper learning curve.
Simply create (or edit) the file by typing:

    nano submit.sh 

or 

    vi submit.sh 

For an example that shows how to run a **python** job on the compute nodes click [here](python_example.md)

For a **matlab** job example click [here](matlab_example.md)

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
