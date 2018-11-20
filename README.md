# ctsm-dev
dev version of Community Terrestrial Systems Model (includes the Community Land Model of CESM)

## out of the box run on abel
CLONE from GitHub

    git clone -b release-clm5.0 https://github.com/NordicESMhub/ctsm.git

LOAD necessary modules

    module load python2/2.7.10
    module load cesm
    module load git


CHECK loaded modules
    
    module list

LOAD externals of CTSM (FATES and so on)

    ./manage_externals/checkout_externals
    
### Set inputdata for all your cases

    cd ~/ctsm/cime/scripts
    ./link_dirtree $CESM_DATA /work/users/$USER/inputdata


### create new case

navigate to ~/ctsm/cime/scripts/

we try two new cases
1) global 
NB! this new case is connected to project nn2806k (for your own project, change the project code)


        ./create_newcase --case ../../../ctsm_cases/fates_f19_g17 --compset 2000_DATM%GSWP3v1_CLM50%FATES_SICE_SOCN_MOSART_SGLC_SWAV --res f19_g17 --machine abel --run-unsupported --project nn2806k
    
2) single cell
    
        ./create_newcase --case ../../../ctsm_cases/fates_1x1 --compset 2000_DATM%GSWP3v1_CLM50%FATES_SICE_SOCN_MOSART_SGLC_SWAV --res CLM_USRDAT --machine abel --run-unsupported --project nn2806k
    
### set up new case

    ./case.setup

IMPORTANT: all work files have to be kept in work directory which is
/work/users/USERNAME 
    
    mkdir /work/users/USERNAME/inputdata/atm/datm7

link atmospheric data from existing directory

	ln -s /work/databases/geo/cesm/inputdata/atm/datm7/atm_forcing.datm7.GSWP3.0.5d.v1.c170516


### case build
    
    ./case.build 

### run case

    ./case.submit 


