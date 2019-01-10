# Tutorial on how to create surface/atm data for single point
## process has several steps
	
	A) GENERATE SCRIPT / MAP / DOMAIN
	B) GENERATE ATMOSPHERIC FORCING
	C) COMPILE FATES

## navigate to "/cluster/home/$USERNAME/ctsm/tools"

read the readme file
 
## changing will take place in 3 folders
	
	1) mkmapgrids/
	2) mkmapdata/ 
	3) mksurfdata_map/

## 1 create export to name variables, which will consecutively be changed in 'mkscripgrid.ncl' file in 	
/cluster/home/peterhor/ctsm/tools/mkmapgrids/

### Description of changed variables

	PTNAME    ! name of your grid
	S_LAT     ! Southern latitude corner
	N_LAT     ! Northern latitude corner
	E_LON     ! Eastern longitude corner
	W_LON     ! Western longitude corner  
	GRIDFILE  ! Output filename


	  export PTNAME=1x1Jotunheimen
	  export S_LAT=61.48457
	  export N_LAT=61.48668
	  export E_LON=8.84752
	  export W_LON=8.84346
	  export GRIDFILE='Jotunheimen_1.nc'
	  
### navigate to /cluster/home/peterhor/ctsm/tools/mkmapgrids/

		module load ncl

### run file to make grid
		
		ncl mkscripgrid.ncl
		
## 2 editing file regridbatch.sh in ctsm\tools\mkmapdata
change ntasks from 8 to 1
	
	#SBATCH --ntasks=1

in the file change line 31 to point to directory where home data
	
	export CSMDATA=/work/users/peterhor/inputdata

line 35 change to 1x1
line 36 change to location name where the grid NC file is created in previous process

	GRIDFILE="/cluster/home/$USERNAME/ctsm/tools/mkmapgrids/Jotunheimen_1.nc"
	
### change permission of executable .sh files in mkmapdata folder
	
	chmod a+x mkmapdata.sh
	chmod a+x regridbatch.sh
	
### run regridbatch file

	sbatch regridbatch.sh
		
### check your progress
	
	squeue -u $USERNAME
### check also process file slurm****.out in the mkmapdata folder
  
