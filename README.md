# ECCO-cloud
ECCO (MITgcm) in the cloud v1.0

[![DOI](https://zenodo.org/badge/76081884.svg)](https://zenodo.org/badge/latestdoi/76081884)

This repository serves as instructions and pointers to run ECCO in the cloud.  The tools and snapshots made available allow a user to easily spin up a cluster of machines in AWS and attach then to an EBS volume containing ECCO in a ready to run state.  The hope is that researchers without access to physical clusters as well as researchers without the time or skills to configure a cluster will still have access to software that can help drive Ocean Science forward.

Requirements:
* An AWS account (we will extend to support other cloud services in the future)
* A machine running starcluster (software found here http://star.mit.edu/cluster/).  This can be a laptop or even a micro EC2 instance using the starcluster AMI.

Intructions:
* In your amazon account, create an EBS volume from the public snapshot: snap-de1b5169 
* Install star cluster on a machine (i.e. your laptop).
* Use the file 'config' from this repository as the configuration file and input required info into fields with ***:
    ** AWS Credentials
    ** EC2 Keypair
    ** EBS Volumes (use the id from the ebs volume you created from the snapshot in step 1)
* Make any other changes you desire to the config file
* Create a new cluster with the star cluster command:  'starcluster start -c smallcluster cluster'
* Verify
    ** cluster now running in AWS master,node001,...,node00n 
* Login to the master node and change directories to /mitgcm/MITgcm to find data and model
* Compile ECCO for 96 CPUs using eccov4 instructions found at http://wwwcvs.mitgcm.org/viewvc/MITgcm/MITgcm_contrib/gael/verification/eccov4.pdf?view=co
* Switch user to the startcluster associated user sgeadmin: 'sudo su - sgeadmin'
* Run ECCO using the command:  'mpiexec -np 96 -host master,node001,node002 ./mitgcmuv &'
* Verify model running on master and nodes (using the command: 'top')

Note that it is unclear that there is any benefit to running the above software with more than 96 cpu's.

If you have questions, feedback, or suggestions--please contact us at (mark at camclimate.org).



