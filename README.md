# ECCO-cloud
ECCO (MITgcm) in the cloud v1.0

This repository serves as a pointer to Amazon AMIs and EBS snapshots which allow a user to easily spin up a cluster of machines and attach to an EBS volume containing ECCO in order to run this ocean state estimate in the cloud.  The hope is that researchers without access to physical clusters and researchers without IT skills will still have access to this tool in order to push forward Ocean Science.

Requirements:
* An AWS account (we will extend to support other cloud services)
* A machine running starcluster (software found here http://star.mit.edu/cluster/).  This can be a laptop or a micro EC2 instance using the starcluster AMI.

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
* Compile ECCO using eccov4 instructions found at http://wwwcvs.mitgcm.org/viewvc/MITgcm/MITgcm_contrib/gael/verification/eccov4.pdf?view=co
* Switch user to the startcluster associated user sgeadmin: 'sudo su - sgeadmin'
* Run ECCO using the command:  'mpiexec -np 96 -host master,node001,node002 ./mitgcmuv &'
* Verify model running on master and nodes (using the command: 'top')

Note that it is unclear that there is any benefit to running the above software with more than 96 cpu's.



