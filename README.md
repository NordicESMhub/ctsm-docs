# ctsm-dev
dev version of Community Terrestrial Systems Model (includes the Community Land Model of CESM)

## out of the box run on abel
CLONE from GitHub (run only first time, or to update clm/ctsm version), in home folder

    git clone -b release-clm5.0 https://github.com/NordicESMhub/ctsm.git

LOAD necessary modules in .bashrc (only first time)

    emacs .bashrc & 
add these three modules 

    module load python2/2.7.10
    module load cesm
    module load git
save and close

CHECK loaded modules
    
    module list

LOAD externals of CTSM (FATES and so on; only necessary first time), in folder ~/ctsm

    ./manage_externals/checkout_externals
    
NB! Fates is not automatically checked out with the latest version (as it is still under development), and this has to be done manually. 
Follow https://github.com/NordicESMhub/ctsm-dev/blob/master/Updating_FATES (based on https://github.uio.no/huit/clm5.0_notes/issues/26 and https://github.com/ESCOMP/ctsm/wiki/Protocols-on-updating-FATES-within-CTSM)

    
### Set inputdata for all your cases
(only first time)

    cd ~/ctsm/cime/scripts
    ./link_dirtree $CESM_DATA /work/users/$USER/inputdata

### Set your Notur project account


NB! this example is connected to project nn2806k (for your own project, change the project code):

     export CESM_ACCOUNT=nn2806k

### create new case

navigate to ~/ctsm/cime/scripts/

we try two new cases

./query_config --compsets (to see available compsets --> choose I... for clm compsets)
./query_config --grids (to see available resolutions)

1) global (fates must be updated before use)

        ./create_newcase --case ../../../ctsm_cases/fates_f19_g17 --compset 2000_DATM%GSWP3v1_CLM50%FATES_SICE_SOCN_MOSART_SGLC_SWAV --res f19_g17 --machine abel --run-unsupported --project $CESM_ACCOUNT
    
or other example without fates

        ./create_newcase --case ~/cases/I2000Clm50BgcCruGs  --compset I2000Clm50BgcCruGs   --res f19_g17 --machine abel --run-unsupported --project geofag


2) single cell (out of the box for brazil)

        ./create_newcase --case ~/cases/brazil_I1PtClm50SpGs --compset I1PtClm50SpGs  --res  1x1_brazil --machine abel --run-unsupported --project geofag

or other compset

         ./create_newcase --case ~/cases/brazil_I2000Clm50BgcCruGs --compset I2000Clm50BgcCruGs  --res  1x1_brazil --machine abel --run-unsupported --project geofag


   
### set up new case
navigate to the case, e.g. ~/ctsm_cases/fates_f19_g17

1) check the configuration

./xmlquery --l (--l list --f file) 

eg 

        ./xmlquery STOP_OPTION
2) Change configuration

        ./xmlchange STOP_OPTION=ndays (nyears, nmonths)
        ./xmlchange STOP_N=5 (then 5 days)
        ./xmlchange NTASKS=1 (number of CPU's, can be increased if excitation error)
or edit the xml files is another way to change these parameters

3) 

           ./case.setup  (--reset)
        
4) edit user_nl_clm   
add this below

        hist_mfilt=5 (number of output files
        hist_nhtfrq=-24 (means daily outputs)

### case build
    
    (./case.build --clean)
    ./case.build
### run case

    ./case.submit 


