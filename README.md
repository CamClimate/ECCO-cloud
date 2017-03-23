# ECCO-cloud
ECCO (MITgcm) in the cloud v1.0

[![DOI](https://zenodo.org/badge/76081884.svg)](https://zenodo.org/badge/latestdoi/76081884)

This repository serves as instructions and pointers to run ECCO in the cloud.  The tools and snapshots made available allow a user to easily spin up a cluster of machines in AWS and attach them to an EBS volume containing ECCO in a ready to run state.  The hope is that researchers without access to physical clusters as well as researchers without the time or skills to configure a cluster will still have access to software that can help drive Ocean Science forward.

Requirements:
* An AWS account (we will extend to support other cloud services in the future)
* A machine running starcluster (software found here http://star.mit.edu/cluster/). Below we include instructions for using an AWS t2.nano instance of “StarCluster_Ubuntu_16.04_Public - ami-040b6113”.

Intructions:
1. In your amazon account, create an EBS volume from the public snapshot: snap-0bb98acc10236f81f.  This is a storage volume that contains the software and inputs needed to run ECCO. 
2. Launch and connect to a t2.nano instance with the Starcluster software in order to launch and terminate your cluster.  (We suggest using the starcluster community AMI on ubuntu 16.04 with id: ami-040b6113.)
3. Download a local copy of the ECCO-cloud repo by typing the following command:
```
git clone https://github.com/CamClimate/ECCO-cloud
```
4. Create the startcluster directory and copy the ‘ECCO-cloud/config' file into it by typing the following commands:
```
mkdir .starcluster
cp ECCO-cloud/config .starcluster/
```
5. Edit the '.starcluster/config' file (using vi or the editor of your choice) in order to replace the following place holders:
```
vi .starcluster/config
```
   * replace YOUR_AWS_ACCESS_KEY_ID with your AWS access key ID (found under "My Sercurity Credentials" under the menu with your name in AWS)
   * replace YOUR_AWS_SECRET_ACCESS_KEY_ID with your AWS secret access key ID (downloaded at the time of creation of your access key)
   * replace YOUR_AWS_USER_ID with your AWS user ID (found under "My Account" under the menu with your name in AWS)
   * replace YOUR_VOLUME_ID with the volume ID corresponding to the EBS volume created from snap-de1b5169 in the first step.
6. Generate a key pair by typing the follwing command:
```
starcluster createkey myclusterkey -o ~/.ssh/myclusterkey.rsa
```
7. Create your compute cluster by typing the following command:
```
starcluster start -c smallcluster myfirstcluster
```
8. Verify in the AWS EC2 console that cluster is now running. Using the default configuration, there will be three new instances running with names master, node001, and node002.
9. Once it is initialized, login to the master node of your cluster by typing the following command:
```
starcluster sshmaster myfirstcluster
```
10. Compile and setup the ECCO model run for 96 CPUs by typing these commands from Fig. 2 of the eccov4.pdf user guide found at [Forget, G. (2016). ECCO v4 r2 user guide and model setup. Zenodo. http://doi.org/10.5281/zenodo.225777](http://doi.org/10.5281/zenodo.225777):
```
cd /mitgcm/MITgcm/mysetups/ECCO_v4_r2/build/
../../../tools/genmake2 -mods=../code -optfile ../../../tools/build_options/linux_amd64_gfortran -mpi
make depend
make -j 4
cd ../run
ln -s ../build/mitgcmuv .
ln -s ../input/* .
ln -s ../input_fields/* .
ln -s ../inputs_baseline2/input*/* .
ln -s ../forcing_baseline2 .
```
11. Switch user to the startcluster associated user "sgeadmin" by typing the following command:
```
su sgeadmin
```
12. Run ECCO by typing the following commands:
```
mpiexec -np 96 -host master,node001,node002 ./mitgcmuv &
```
13. Verify that model is running on master and nodes, e.g., by using the 'top' command
```
top
```
14. Keep track of the model run as it progresses (see how many of the monthly output files exist) by typing the following command:
```
ls -1 state_2d_set1.0*data
```
15. By the end of the 20 year model run there should be 240 monthly output files.  Once the model run has completed, verify that STDOUT.0000 ends with 'PROGRAM MAIN: Execution ended Normally' by typing the following command: 
```
tail STDOUT.0000' 
```  
16. Make sure to terminate the cluster once you are done (to stop paying for it!) by typing the following command from the first instance you created.:
```
starcluster terminate myfirstcluster
```
17.  When you are done with your initial instance, terminate it as well from the AWS website to avoid further charges.  (At the time of this writing, this machine costs ~$5/month in the U.S.)
18. Your results are still available on the volume created in step 1.  Simply attache the volume to any AWS instance to access the data stored on it.

Note: distinct cluster name (‘myfirstcluster’ above) and key name (`myclusterkey’) may be needed each time

Note: it is unclear that there is any benefit to running the above software with more than 96 cpu's.

If you have questions, feedback, or suggestions--please contact us at (cloud-support at camclimate.org).



