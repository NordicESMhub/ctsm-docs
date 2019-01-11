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
  


## B) Making surface data
go to > $/tools/mksurfdata_map

library for ncdf has to be consistent

	module load netcdf.intel/4.3.3.1  
adjust library paths in the file /cluster/home/peterhor/ctsm/tools/mksurfdata_map/src/Makefile.common
line 33
LIB_NETCDF := /cluster/software/VERSIONS/netcdf.intel-4.3.3.1/lib
and line 35
INC_NETCDF := /cluster/software/VERSIONS/netcdf.intel-4.3.3.1/include

these paths are found in information about the specific module

	module show netcdf.intel/4.3.3.1

Then run gmake again

	gmake
	
then go back to /cluster/home/peterhor/ctsm/tools/mksurfdata_map/ and run mksurfdata.pl

check the options

	./mksurfdata.pl --help

#IMPORTANT!!! this file sets up the details about your surface grid
check, examine, read what could be potentially relevant to change

mksurfdata.pl -res usrspec -usr_gname <user_gname> -usr_gdate <user_gdate>  [OPTIONS]
-res specified by user
-usr_gname 1x1_km (in this case 1x1 otherwise in mkmapdata files ex: map_5x5min_nomask_to_1x1_km_nomask_aave_da_c190110.nc)
-usr_date 190110 is the date when created
-usr_mapdir path to mkmapdata
-dinlc directory for inputdata./
-no-crop exclude crop PFT form file
-allownofile  #!Allow the script to run even if one of the input files does NOT exist.
-years
also check other useful options for exampe dynamic pft -dynpft or -hirespft

	./mksurfdata.pl -res usrspec -usr_gname 1x1_km -usr_gdate 190110 -usr_mapdir /cluster/home/peterhor/ctsm/tools/mkmapdata -dinlc /work/users/peterhor/inputdata -allownofile -years 2000 -no-crop


check dataset
	
	module load ncview
	ncview surfdata_1x1_km_hist_16pfts_Irrig_CMIP6_simyr2000_c190111.nc
	ncdump surfdata_1x1_km_hist_16pfts_Irrig_CMIP6_simyr2000_c190111.nc 
save to file

	ncdump surfdata_1x1_km_hist_16pfts_Irrig_CMIP6_simyr2000_c190111.nc >test.cdl

do changes in test.cdl in texteditor and save to ncdf
 
	ncgen -o test.nc test.cdl


# create domain
/cluster/home/peterhor/ctsm/cime/tools/mapping/gen_domain_files/
follow INSTALL
(1) $ cd src
(2) $ ../../../configure --machine abel --macros-format Makefile --mpilib mpi-serial
Bash users:
(3) $ (. ./.env_mach_specific.sh ; gmake)

./gen_domain -m <filemap>
             -o <gridocn>
             -l <gridlnd>
             [-p set_fv_pole_yc]
             [-c <user comment gen_domain.nml>]


filemap is generated as follows:
mk




automatization of these proceses can be adapted from :
https://github.uio.no/huit/single_cell_experiment/blob/master/run_script_c14_plots


git status
git add
git commit

