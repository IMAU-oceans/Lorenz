# starting a Python job on the compute nodes:

Suppose you want to run a python program test.py from directory /nethome/my/run/directory/
\
on 1 core of Lorenz and you think the job will take at most 10 hours to complete. 

Say this python program needs one of your conda environments which you called 'envname'

In this directory then make a file:  submit.sh (or whatever you want to call it) with contents:

    #!/bin/bash -l
    #
    #SBATCH -J PYTEST           # the name of your job   
    #SBATCH -p normal           # request normal partition, job takes > 1 hour (this line can also be left out because 'normal' is the default)  
    #SBATCH -t 10:00:00         # time in hh:mm:ss you want to reserve for the job
    #SBATCH -n 1                # the number of cores you want to use for the job, SLURM automatically determines how many nodes are needed
    #SBATCH -o log_pytest.%j.o  # the name of the file where the standard output will be written to. %j will be the jobid determined by SLURM
    #SBATCH -e log_pytest.%j.e  # the name of the file where potential errors will be written to. %j will be the jobid determined by SLURM
 
    module load miniconda
    eval "$(conda shell.bash hook)"  # this makes sure that conda works in the batch environment 
    
    cd /nethome/my/run/directory/
    conda activate envname      # this passes your conda environment to all the compute nodes
    srun python3 test.py
     
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
    
Currently there are 3 versions of python installed on Lorenz. Typing: 

    python3
    
will start python 3.9.5

    python3.8
    
will start python 3.8.13

    python3.6
    
will start python 3.6.8


    

