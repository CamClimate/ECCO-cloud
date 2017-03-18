# ECCO-cloud
ECCO (MITgcm) in the cloud v1.0

[![DOI](https://zenodo.org/badge/76081884.svg)](https://zenodo.org/badge/latestdoi/76081884)

This repository serves as instructions and pointers to run ECCO in the cloud.  The tools and snapshots made available allow a user to easily spin up a cluster of machines in AWS and attach them to an EBS volume containing ECCO in a ready to run state.  The hope is that researchers without access to physical clusters as well as researchers without the time or skills to configure a cluster will still have access to software that can help drive Ocean Science forward.

Requirements:
* An AWS account (we will extend to support other cloud services in the future)
* A machine running starcluster (software found here http://star.mit.edu/cluster/). Below we include instructions for using an AWS t2.micro instance of “Amazon Linux AMI 2016.09.1 (HVM), SSD Volume Type - ami-9be6f38c”.

Intructions:
* In your amazon account, create an EBS volume from the public snapshot: snap-de1b5169.  This is a storage volume that contains the software and inputs needed to run ECCO. 
* Launch and connect to a t2.micro instance with the Starcluster software in order to launch and terminate your cluster.  (We suggest using the starcluster community AMI with ubuntu 16.04 with id: ami-040b6113.)
* Download a local copy of the ECCO-cloud repo by typing the following command:
```
git clone https://github.com/CamClimate/ECCO-cloud
```
* Copy the ‘ECCO-cloud/config' file to a directory dedicated to starcluster by typing the following commands:
```
mkdir starcluster
cp ECCO-cloud/config starcluster/
```
* Edit the 'starcluster/config' file, e.g. using vi, in order to replace the following place holders:
  * YOUR_AWS_ACCESS_KEY_ID    with your AWS access key ID (found under "My Sercurity Credentials" under the menu with your name in AWS)
  * YOUR_AWS_SECRET_ACCESS_KEY_ID    with your AWS secret access key ID (downloaded at the time of creation of your access key)
  * YOUR_AWS_USER_ID    with your AWS user ID (found under "My Account" under the menu with your name in AWS)
  * YOUR_VOLUME_ID    with the volume ID corresponding to the EBS volume created from snap-de1b5169 in the first step.
  * yourclusterkey    with an original name of your choosing (all 3 instances)
* Install the starcluster software (see http://star.mit.edu/cluster/) by typing:
  sudo easy_install StarCluster
* Generate the necessary key pair by substituting “yourclusterkey” with the name 
  you chose earlier in the following template:
  starcluster createkey yourclusterkey -o ~/.ssh/yourclusterkey.rsa
* Create the cluster by typing:
  starcluster start -c smallcluster yourfirstcluster
* Verify in the AWS EC2 console that cluster is now running (master,node001,...,node00n)
* Login to the master node:
  starcluster sshmaster yourfirstcluster
* change directory to the mounted volume:
  cd /mitgcm
* Compile and setup the ECCO model run for 96 CPUs by following instructions in Fig. 2 of the
  eccov4.pdf user guide found at http://doi.org/10.5281/zenodo.225777 [Forget, G. (2016). 
  ECCO v4 r2 user guide and model setup. Zenodo. http://doi.org/10.5281/zenodo.225777]
* Switch user to the startcluster associated user sgeadmin:
  sudo su - sgeadmin
* Run ECCO:
  cd /mitgcm/MITgcm/mysetups/ECCO_v4_r2/run
  mpiexec -np 96 -host master,node001,node002 ./mitgcmuv &
* Verify that model is running on master and nodes, e.g., using the 'top' command
* Keep track of the model run as it progresses by typing 'ls -1 state_2d_set1.0*data' to see 
  how many of these monthly output files exist; by the end of the 20 year model run there should 
  be 240 of them; once the model run has completed the command 'tail STDOUT.0000' should show 
  'PROGRAM MAIN: Execution ended Normally' as the last line
* Make sure to terminate the cluster once you are done (to stop paying for it!):
  starcluster terminate yourfirstcluster

Note: distinct cluster name (‘yourfirstcluster’ above) and key name (`yourclusterkey’) may be needed each time
Note: it is unclear that there is any benefit to running the above software with more than 96 cpu's.

If you have questions, feedback, or suggestions--please contact us at (mark at camclimate.org).



