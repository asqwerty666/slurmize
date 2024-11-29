# slurmize.pl

This is a very simple perl script that try to expose a very simple way to launch jobs into a SLURM cluster. It uses an external perl module (SLURMACE.pm) so we also provide a shell script to download and install it into your system. 

## Install
- Download and run the *install\_slurmace.sh* script
- put the slurmize.pl script wherever you need it an run it
- enjoy

## usage

This script just read a simple plain text file that contains the orders that you want to parallelize inthe cluster. Let's say that you want to run Freesurfer recon-all procedure over 15 subjects. Then you write a file like this,

	recon-all -autorecon-all -subjid subject01
	recon-all -autorecon-all -subjid subject02
	...
	...
	...
	recon-all -autorecon-all -subjid subject15

and save it as *recon_subject.txt*. Now you just simple do,

	./slurmize.pl recon_subject.txt

and the script automagicallly make a directory named _slurm_, creates a separate sbatch script for each subject and launch it into the schedule manager and creates a warning script that send an email to warn you when the execution of all subjects is over. Output for each separate job it is stored in the _slurm_ directory also, so in case something goes wrong, you can check the output by the job id.

### Debugging your runs

Sometimes things go wrong when you generate the list of jobs to send into the cluster. So, to provide a way to check if everything it is OK before you run your jobs, we added the *-g* option. If you run,

	./slurmize.pl -g recon_subject.txt 

the script generates all the structure inside the *slurm* directory but runs nothing. This way you can review what is going to be executed into your nodes.
