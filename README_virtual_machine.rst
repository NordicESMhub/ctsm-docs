Setting up a new virtual machine using uh-iaas:
===============================================

see also http://docs.uh-iaas.no/en/latest/index.html for instructions. Login or set up new user and project using a web form (see http://docs.uh-iaas.no/en/latest/project-application.html#proper-projects)

Step 1: Create a key pair. Open command prompt/terminal to generate key pair

	ssh-keygen
	keyname	

The Key is a .pub file and will appear in the file path where you entered your keyname. The key then has to be imported to the uh-iaas. Go to uh-iaas dashboard – key pairs – import key – enter keyname and copy key from the .pub file (open in notepad). 

Step 2: Create an instance (virtual machine). In uh-iaas dashboard, go to Instance – launch instance.

Fill inn the follwing and click 'next' on the rest/accept defaults:
Instance name (e.g. ctsm_test),
source (e.g. GOLD Ubuntu 18.04 LTS), 
Flavor (check with your project quota – choose largest possible?), 
Network (use dualStack for some reason…), 
[security network will be chosen later,] 
Key pair (same as created above, should show up in list) -> launch instance

Step 3: Create and define a security group. Go to Network – security groups – create security group
Name it e.g. SSH and ICMP – create
Manage rules – add rule: Rule SSH (choose from dropdown menu) – add
Manage rules – add rule: Rule ALL ICMP (choose from dropdown menu) – add

Go to compute – instance – actions: edit secutiry groups (choose from dropdown menu) – click + for the security group you created – save

Congrats! Your virtual machine should now work. 

Connecting to your instance/virtual machine: 

open command prompt/terminal and enter “ssh -i instancename ubuntu@ipaddress” (copy the ip address from the instance dashboard), e.g.

	ssh -i ctsm_test ubuntu@158.37.63.157
	

Prepare the virtual machine for CLM simulations and python analyzing:
=====================================================================

1: Login on UH-IAAS
    -create a volume : 200GB  (will contain input data etc)
    -go to instance and attach the volume just created
    
2: Connect on the virtual machine via terminal: ssh -Y -i winterproof ubuntu@158.37.63.152

	lsblk (shows the volume        (called sdb) and the instance)
	sudo mkfs.ext4 /dev/sdb        (Create an ext4 File System)
	sudo mkdir -p /opt/uio         (Create a new directory to mount your new volume)
	sudo mount /dev/sdb /opt/uio   (Mount the new volume)
	df -h /opt/uio                 (Check your new volume)
	
	cd /opt
	sudo chown ubuntu uio
	sudo chgrp ubuntu uio
    
3: install anaconda3
	
	wget https://repo.continuum.io/archive/Anaconda3-2018.12-Linux-x86_64.sh
	batch ...sh
	Yes
	opt/uio/anaconda3
	yes
	no
	exit
	ssh -Y -i ...
	sudo apt-get install x11-apps
	sudo apt-get update
	sudo apt-get install firefox
	
	create environnement_python.yml (see environnement_python.yml)
	
	anaconda-navigator (from where you can launch Jupyter notebook, and add environment)
	
4: Packages needed to run CLM	
	
	sudo apt-get install libmpich-dev
	sudo apt-get install libnetcdf-dev
	
    


