# Tutorial on how to create surface/atm data for single point
This process may be necessary before building the case (see guide in NordicESMhub/ctsm-dev/README.md).
Make sure you are working on your own branch - you need three branches when running CTSM with FATES; one for ctsm, one for cime and one for fates. (Check current branch with git branch)

## process has several steps
	
	A) GENERATE SCRIPT / MAP / SURFACE DATA / DOMAIN
	B) GENERATE ATMOSPHERIC FORCING
	C) COMPILE FATES

## A) Generate script / map / domain
navigate to "/cluster/home/$USERNAME/ctsm/tools"

read the readme file
 
## changing will take place in 3 folders:
	
	1) mkmapgrids/
	2) mkmapdata/ 
	3) mksurfdata_map/

## 1) create export to name variables, which will consecutively be changed in 'mkscripgrid.ncl' file in 	
/cluster/home/peterhor/ctsm/tools/mkmapgrids/
The purpose is to define variable values (coordinates for your localities) that mkscripgrid.ncl needs.

mknoocean can also be used to create this so this step can be skipped

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

## 2) editing and running regridbatch.sh in ctsm\tools\mkmapdata 

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
  


## 3) Making surface data
go to  ctsm/tools/mksurfdata_map/src

library for ncdf has to be consistent

	module load netcdf.intel/4.3.3.1  
	
find library by

	module list #find exact name of the netcdf type you use
	module show netcdf.intel/4.3.3.1 # show information about it, look for LD_LIBRARY_PATH, e.g./cluster/software/VERSIONS/netcdf.intel-4.3.3.1/lib

adjust library paths in the file /cluster/home/peterhor/ctsm/tools/mksurfdata_map/src/Makefile.common

line 33		LIB_NETCDF := /cluster/software/VERSIONS/netcdf.intel-4.3.3.1/lib
and line 35 	INC_NETCDF := /cluster/software/VERSIONS/netcdf.intel-4.3.3.1/include

Then run gmake again

	gmake
	
then go back to /cluster/home/peterhor/ctsm/tools/mksurfdata_map/ and run mksurfdata.pl

check the options

	./mksurfdata.pl --help

#IMPORTANT!!! this file sets up the details about your surface grid
check, examine, read what could be potentially relevant to change. Peter&Eva changed these:

	# -res usrspec 
	# -usr_gname 1x1_km # in this case 1x1 otherwise in mkmapdata files e.g. map_5x5min_nomask_to_1x1_km_nomask_aave_da_c190110.nc)
	# -usr_gdate 190110 # time mapping files were generated (10.jan.2019)
	# -usr_mapdir      # path to mkmapdata
	# -dinlc          # directory for the inputdata	
	# -allownofile    # Allow the script to run even if one of the input files does NOT exist.
	# -no-crop	# Include if locality has no agricultural land (exclude crop PFT)
	# -years 2000	# when the model is run (default is 1850-2000 which will give error if e.g. the input data for 1850 is not available)
	# also check other useful options for exampe dynamic pft -dynpft or -hirespft

	./mksurfdata.pl -res usrspec -usr_gname 1x1_km -usr_gdate 190110 -usr_mapdir /cluster/home/peterhor/ctsm/tools/mkmapdata -dinlc /work/users/peterhor/inputdata -allownofile -years 2000 -no-crop

check dataset
	
	module load ncview
	ncview surfdata_1x1_km_hist_16pfts_Irrig_CMIP6_simyr2000_c190111.nc
	ncdump surfdata_1x1_km_hist_16pfts_Irrig_CMIP6_simyr2000_c190111.nc 
	
save to file

	ncdump surfdata_1x1_km_hist_16pfts_Irrig_CMIP6_simyr2000_c190111.nc >test.cdl

do changes in test.cdl in texteditor, e.g. one of the numbers after line 375 ORGANIC = (soil carbon), and save to ncdf
 
	ncgen -o test.nc test.cdl


# create domain
the model uses domain files to get information about the locality (gridcell), and links to other components - separate domain files are needed for ocean, atmosphere etc for running fully coupled model. We only use the land domain.

go to  /cluster/home/peterhor/ctsm/cime/tools/mapping/gen_domain_files/
read readme
read install

	cd src
	../../../configure --machine abel --macros-format Makefile --mpilib mpi-serial 
echo $0 # to find if bash or csh

	. ./.env_mach_specific.sh 
	gmake

generate a mapping file for the gen_domain function - go to /cluster/home/evaler/ctsm/tools/mkmapdata/

	./mknoocnmap.pl --help

need to add center coordinate, a name (invent one, e.g. jotunheimen_1 or aurland) and grid cell size (subtract smallest N/S and W/E coordinate from the largest for dx/dy).

	# module load ncl
	./mknoocnmap.pl -centerpoint 60.90439865,7.2851082 -name aurland -dx 0.0242865 -dy 0.0459524
this generates three files. Two are put in /cluster/home/evaler/ctsm/tools/mkmapgrids/, the last (insert as <filemap> in next step) is in /ctsm/tools/mkmapdata.


go back to /ctsm/cime/tools/mapping/gen_domain_files/

	# gen_domain 
	# -m <filemap> # mapping file name for atm to ocean, e.g. map_aurland_noocean_to_aurland_nomask_aave_da_190111.nc
        # -o <gridocn> # invent a name, e.g. 1x1_ocn_aurland
        # -l <gridlnd> # invent a name, e.g. 1x1_lnd_aurland
        # [-p set_fv_pole_yc]
        # [-c <user comment gen_domain.nml>]
	
	
		./gen_domain -m ~/ctsm/tools/mkmapdata/map_aurland_noocean_to_aurland_nomask_aave_da_190111.nc -o 1x1_aurland_ocn -l 1x1_aurland_ocn
to check if the coordinates are consistent, go to /cluster/home/evaler/ctsm/tools/mkmapgrids/ and use 

	ncdump SCRIPgrid_1x1_FATES_test_nomask_c190110.nc # this is the 'correct'/hardest to change one (set in previous steps)
	ncdump SCRIPgrid_aurland_nomask_c190111.nc # if they are not the same, check that dy dx is not switched etc.
 




## B) Atmospheric forcing data files were generated by HUI so far. It is possible to use global atmospheric forcing data, but this is not good for proper research.
(NB these will be deleted by UiO periodically, so keep backups and upload here when needed:)
go to /work/users/evaler/inputdata/atm/datm7/ 
add forcing files to a new folder: atm_forcing_data

### For each new case some files must be modified. Go to the case directory /cluster/home/peterhor/ctsm_cases/fates_jotunheimen/
always check in these files:
env_mach_pes.xml - change CPU if necessary (1 is good for single cell)
env_batch.xml - change the following:
	line 76     <entry id="JOB_WALLCLOCK_TIME" value="01:30:00"> # set to low number by defult (for testing, will be bumped up in run queue), adjust for how long you need for the model run

env_run.xml - change/check the following:
	line 113	<entry id="RUN_STARTDATE" value="0001-01-01"> # change to your start up date, e.g. 1999-01-01
	line 125 	<entry id="STOP_OPTION" value="ndays"> # number of days, check line 127 for possible values
	line 132 	<entry id="STOP_N" value="5"> # 
	line 222 	<entry id="CONTINUE_RUN" value="FALSE"> # sometimes, change if you need to stop a specific year and then continue from that year. 
	line 297 	<entry id="GMAKE_J" value="1"> # set to 8 to make compiling faster
	line 411	<entry id="ATM_DOMAIN_FILE" value="UNSET"> # set to the domain file generated above, e.g. domain.lnd.1x1_aurland_ocn_1x1_aurland_ocn.190111.nc
look at the path in line 415, copy the domain file into that folder
	line 419 	    <entry id="LND_DOMAIN_FILE" value="domain.lnd.1x1_aurland_ocn_1x1_aurland_ocn.190111.nc"> #
	line 766 	    <entry id="DIN_LOC_ROOT" value="/work/users/$USER/inputdata"> # where the model looks for input data
	line 1292-1302 	    <entry id="DATM_CLMNCEP_YR_ALIGN" value="1"> # change the years to something that fits the experiment, e.g. 1999 and start 1990 and end 2010 (of the cycle).
	line 1309 	    <entry id="LND_TUNING_MODE" value="clm5_0_GSWP3v1"> # change based on how the atm_forcing is made / what the  atmospheric data based on
copy the surface data file into the surfdata_map folder:
	
	cp ~/ctsm/tools/mksurfdata_map/surfdata_1x1_km_hist_16pfts_Irrig_CMIP6_simyr2000_c190111.nc /work/users/evaler/inputdata/lnd/clm2/surfdata_map/
back to the env_run.xml:
	line 1354    <entry id="CLM_USRDAT_NAME" value="1x1_km"> # give a name that will identify the file, here 1x1, to describe file (not file name nor path)


### go to /cluster/home/evaler/ctsm/bld/namelist_files/ 
open namelist_defaults_usr_files.xml
add this line after line 27:
	<fsurdat phys="clm5_0">lnd/clm2/surfdata_map/surfdata_${clm_usr_name}_hist_16pfts_Irrig_CMIP6_simyr${sim_year}_c190111.nc</fsurdat> 
(look at your surface data file name and change according to needs)

### go to /cluster/home/evaler/ctsm/src/utils/
replace clm_nlUtilsMod.F90 with file from Hui

### go to /cluster/home/evaler/ctsm/cime_config/
open buildlib
add the following as a new line 84 (between ..."fire" and ..."utils"):
		os.path.join(lnd_root,"src","fates","parteh"),

### go to /cluster/home/evaler/ctsm/cime/src/components/data_comps/datm/cime_config/
open namelist_definition_datm.xml
look at line 382 for the path where the atm forcing data should be, and move it there, e.g.
from /work/users/evaler/inputdata/atm/datm7/atm_forcing_data/
to /work/users/evaler/inputdata/atm/datm7/1x1_km/CLM1PT_data/
and line 456, if necessary change the value by adding prefix of atm forcing data file names
      <value  stream="CLM1PT.CLM_USRDAT">clm1pt_eva_%ym.nc</value>

## C) build and run the case
### go to the case folder

	./case.setup #--reset
	./case.build #--clean-all and then ./case.setup --reset again

	./case.submit

	squeue -u evaler # check progress (or /.case.run for local run if Abel is very busy and it's a small test run)

* Error with some files when we tried this 11.jan. Hui will look into it.


remember to

	git status 
	git add 
	git commit #commit changes







automatization of these proceses can be adapted from :
https://github.uio.no/huit/single_cell_experiment/blob/master/run_script_c14_plots


