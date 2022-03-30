# starting a Matlab job on the compute nodes:

Suppose you want to run a matlab program mymat.m from directory /nethome/my/run/directory/
\
on 1 core of Lorenz and you think the job will take at most 10 hours to complete. 

In this directory then make a file:  submit.sh (or whatever you want to call it) with contents:

    #!/bin/bash -l
    #
    #SBATCH -J MYMAT             # the name of your job   
    #SBATCH -p normal            # request normal partition, job takes > 1 hour (this line can also be left out because 'normal' is the default)  
    #SBATCH -t 10:00:00          # time in hh:mm:ss you want to reserve for the job
    #SBATCH -n 1                 # the number of cores you want to use for the job, SLURM automatically determines how many nodes are needed
    #SBATCH -o log_mymat.%j.o    # the name of the file where the standard output will be written to. %j will be the jobid determined by SLURM
    #SBATCH -e log_mymat.%j.e    # the name of the file where potential errors will be written to. %j will be the jobid determined by SLURM
 
    module load matlab
    unset DISPLAY                # this avoids showing matlab graphics
    cd /nethome/my/run/directory/
   
    matlab -nodisplay < mymat.m
 
     
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
