# ctsm-dev
dev version of Community Terrestrial Systems Model (includes the Community Land Model of CESM)

## out of the box run
CLONE from GitHub

    git clone -b release-clm5.0 https://github.com/NordicESMhub/ctsm.git

LOAD necessary modules

    module load python2/2.7.10
    module load cesm
    module load git


CHECK loaded modules
    
    module list

LOAD externals of CESM (FATES and so on)

    ./manage_externals/checkout_externals
    
### create new case
navigate to ~/ctsm/cime/scripts/
we try two new cases
1) global 
NB! this new case is connected to project nn2806k (for your own project, change the project code)


        ./create_newcase --case ../../../clm5.0_cases/fates_f19_g17 --compset 2000_DATM%GSWP3v1_CLM50%FATES_SICE_SOCN_MOSART_SGLC_SWAV --res f19_g17 --machine abel --run-unsupported --project nn2806k
    
2) single cell
    
        ./create_newcase --case ../../../clm5.0_cases/fates_1x1 --compset 2000_DATM%GSWP3v1_CLM50%FATES_SICE_SOCN_MOSART_SGLC_SWAV --res CLM_USRDAT --machine abel --run-unsupported --project nn2806k
    
