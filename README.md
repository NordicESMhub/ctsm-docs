# ctsm-dev
dev version of Community Terrestrial Systems Model (includes the Community Land Model of CESM)

## out of the box run on abel
CLONE from GitHub (run only first time, or to update clm/ctsm version), in home folder

    git clone -b release-clm5.0 https://github.com/NordicESMhub/ctsm.git

LOAD necessary modules (run every time)

    module load python2/2.7.10
    module load cesm
    module load git


CHECK loaded modules
    
    module list

LOAD externals of CTSM (FATES and so on; only necessary first time), in folder ~/ctsm

    ./manage_externals/checkout_externals
    
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
1) global 

        ./create_newcase --case ../../../ctsm_cases/fates_f19_g17 --compset 2000_DATM%GSWP3v1_CLM50%FATES_SICE_SOCN_MOSART_SGLC_SWAV --res f19_g17 --machine abel --run-unsupported --project $CESM_ACCOUNT
    
2) single cell
    
        ./create_newcase --case ../../../ctsm_cases/fates_1x1 --compset 2000_DATM%GSWP3v1_CLM50%FATES_SICE_SOCN_MOSART_SGLC_SWAV --res CLM_USRDAT --machine abel --run-unsupported --project $CESM_ACCOUNT
    
### set up new case
navigate to the case, e.g. ~/ctsm_cases/fates_f19_g17

    ./case.setup

### case build
    
    ./case.build 

### run case

    ./case.submit 


