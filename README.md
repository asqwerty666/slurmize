# slurmize.pl

This is a very simple perl script that try to expose a very simple way to launch jobs into a SLURM cluster. It uses an external perl module (SLURMACE.pm) so we also provide a shell script to download and install it into your system. 

## Install
- Download and run the *install\_slurmace.sh* script
- put the slurmize.pl script wherever you need it an run it
- enjoy

## Usage

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

### Customize to your needs

The script should run minimal in any SLURM cluster but maybe you want to adjust some behaviour like the partition, the execution time or something similar. Notice that you can edit some default variables at the bbeginning of the script that can change the paralelization behaviour. 

However this cover only a few posibilities. For a depper customization you should see the SLURMACE documentation (https://github.com/asqwerty666/acenip/blob/main/doc/SLURMACE.md) and edit the definition of the %ptask hash inside the script. It should be easy if the already shown syntax is followed. By example, if you want to add an email address diferent from the local user email you can add something like,

```
	'mail_user' => 'another_email@myserver.com',
```

to the definition of the parallelization hash as,

```
my %ptask = ( 'job_name' => basename($ifile), 
	'cpus' => $cpus_per_proc, 
	'mem_per_cpu' => $mem_per_cpu, 
	'time' => $time,  
	'mail_type' => 'FAIL,TIME_LIMIT,STAGE_OUT', 
	'partition' => $partition, 
	'debug' => $debug,
	'mail_user' => 'another_email@myserver.com', 
);

``` 

### Debugging your runs

Sometimes things go wrong when you generate the list of jobs to send into the cluster. So, to provide a way to check if everything it is OK before you run your jobs, we added the *-g* option. If you run,

	./slurmize.pl -g recon_subject.txt 

the script generates all the structure inside the *slurm* directory but runs nothing. This way you can review what is going to be executed into your nodes.
