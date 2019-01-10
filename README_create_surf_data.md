# Tutorial on how to create surface/atm data for single point
This process may be necessary before building the case (see guide in NordicESMhub/ctsm-dev/README.md).
Make sure you are working on your own branch - you need three branches when running CTSM with FATES; one for ctsm, one for cime and one for fates. (Check current branch with git branch)

## process has several steps
	
	A) GENERATE SCRIPT / MAP / DOMAIN
	B) GENERATE ATMOSPHERIC FORCING
	C) COMPILE FATES

## navigate to "/cluster/home/$USERNAME/ctsm/tools"

read the readme file
 
## changing will take place in 3 folders:
	
	1) mkmapgrids/
	2) mkmapdata/ 
	3) mksurfdata_map/

## 1) create export to name variables, which will consecutively be changed in 'mkscripgrid.ncl' file in 	
/cluster/home/peterhor/ctsm/tools/mkmapgrids/
The purpose is to define variable values (coordinates for your localities) that mkscripgrid.ncl needs.

### Variables that need to be given a value:
	# PTNAME    ! name of your grid
	# S_LAT     ! Southern latitude corner
	# N_LAT     ! Northern latitude corner
	# E_LON     ! Eastern longitude corner
	# W_LON     ! Western longitude corner  
	# GRIDFILE  ! Output filename   # optional


	  export PTNAME=1x1Jotunheimen
	  export S_LAT=61.48457
	  export N_LAT=61.48668
	  export E_LON=8.84752
	  export W_LON=8.84346
	  export GRIDFILE='Jotunheimen_1.nc'  # optional
	  

### run file to make grid
		
		module load ncl
		ncl mkscripgrid.ncl

## 2 editing and running regridbatch.sh in ctsm\tools\mkmapdata 

Download Hui's version of files named regridbatch.sh and mkmapdata.sh to your local computer (found in folder ctsm-dev/files). If on MobaXterm, navigate to the mkmapgrids directory and re-name the original files (e.g. by adding \_original to the name), then upload the two files. 

Changes needed in the new regridbatch.sh to adapt to your folder structure and file names:

	line 11 	#SBATCH --account=uio #if not correct already
	line 14		#SBATCH --ntasks=1 #number of cpus used, change from 8 to 1 for single gridcell cases
	line 31 	export CSMDATA=<inputdatadirectory> # e.g. CSMDATA=/work/users/evaler/inputdata
	line 35 	RES="1x1_km" #change from 5x5 to 1x1 for single-cell run
	line 36 	GRIDFILE="/cluster/home/$USERNAME/ctsm/tools/mkmapgrids/Jotunheimen_1.nc" # the mkmapgrid file we just made in step 1). This must be repeated/changed for new localities/grid cells (name them e.g. Jotunheimen_1, Jotunheimen_2 etc)


	
### change permission of executable .sh files in mkmapdata folder
(This was necessary for Peter but not for Eva)
	
	chmod a+x mkmapdata.sh
	chmod a+x regridbatch.sh
	
### run regridbatch file (NB! this will take several hours)

	sbatch regridbatch.sh
		
### check your progress
	
	squeue -u $USERNAME
	
### check also process file slurm****.out in the mkmapdata folder
  
