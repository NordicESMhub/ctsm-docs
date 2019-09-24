Here, the regional case procedure is explained for running the model over Fennoscandia and Svalbard region at 0.25 degree spatial resolution on Abel.

Required input files (domain and surface data) are already produced and stored under the main inputdata directory. So, start with linking the main inputdata files to your working directory.

::

    cd ~/ctsm/cime/scripts
    ./link_dirtree $CESM_DATA /work/users/$USER/inputdata
    export MYCTSMDATA=/work/users/$USER/inputdata

Definitions
~~~~~~~~~~~
As mentioned earlier in this documentation, you need to define your project account (e.g. nn2806k) before running the model. To be sure that you are using the correct version of netcdf and practical purposes, we will export the paths and some environment variables :

::

    export CESM_ACCOUNT=nn2806k
    export PROJECT=nn2806k
    export CTSMROOT=/cluster/home/$USER/ctsm
    
    export INC_NETCDF=/cluster/software/VERSIONS/netcdf.intel-4.3.3.1/
    export LIB_NETCDF=/cluster/software/VERSIONS/netcdf.intel-4.3.3.1/
    export NETCDF_ROOT=/cluster/software/VERSIONS/netcdf.intel-4.3.3.1
    
Make a case
~~~~~~~~~~~
To run the model with Satellite Phenology mode, do the following

::

    cd $CTSMROOT/cime/scripts
    ./create_newcase -case ~/cases/SCA_SpRun -res CLM_USRDAT -compset I2000Clm50SpGs --machine abel --run-unsupported --project $CESM_ACCOUNT
    

Here, the compset **I2000Clm50SpGs** initialize the model from 2000 conditions with GSWP3 atmospheric forcing data. If you want to run CTSM with BGC mode and force with CRU NCEP v7 data set, use **I2000Clm50BgcCruGs** compset. To see available compset aliases and their long names, type the following :
::

    $CTSMROOT/cime/scripts/query_config --compsets clm

**Note that** you can use only the compsets with **SGLC** (Stub glacier (land ice) component) for single point and regional cases. Otherwise, simulation fails.

Set up the case
~~~~~~~~~~~
Now, we are setting up our test simulation for three years between 2000-2002.

::

    cd ~/cases/SCA_SpRun
    export GRIDNAME=0.25_SCA
    export SCA_DATA=$MYCTSMDATA/share/domains/
    export ATMDOM=domain.0.25x0.25_SCA.nc

    ./xmlchange DIN_LOC_ROOT=$MYCTSMDATA
    ./xmlchange ATM_DOMAIN_PATH=$SCA_DATA,LND_DOMAIN_PATH=$SCA_DATA
    ./xmlchange ATM_DOMAIN_FILE=$ATMDOM,LND_DOMAIN_FILE=$ATMDOM
    ./xmlchange CLM_USRDAT_NAME=$GRIDNAME
    ./xmlchange STOP_OPTION=nyears
    ./xmlchange STOP_N=3
    ./xmlchange NTASKS=16
    ./xmlchange JOB_WALLCLOCK_TIME="03:59:00"
    ./xmlchange PROJECT=$CESM_ACCOUNT
    ./xmlchange RUN_STARTDATE="2000-01-01"
    ./xmlchange RESUBMIT="0"
    ./xmlchange DATM_CLMNCEP_YR_ALIGN="2000"
    ./xmlchange DATM_CLMNCEP_YR_START="2000"
    ./xmlchange DATM_CLMNCEP_YR_END="2002"
    ./xmlchange DOUT_S="FALSE"
    ./xmlchange GMAKE_J="8"
    ./case.setup
    ./preview_run


Customizing the CLM namelist
~~~~~~~~~~~
In order to define the location of surface data file for our Scandinavia region, we add the **fsurdat** setting to the land model's namelist.

::

    cat << EOF > user_nl_clm
    fsurdat="$MYCTSMDATA/lnd/clm2/surfdata_map/surfdata_0.25_SCA_hist_16pfts_Irrig_CMIP6_simyr2000_c190814.nc"
    EOF

If you run your simulation with **BGC** mode, then you need to set another surface data map as follows:

::

    cat << EOF > user_nl_clm
    fsurdat="$MYCTSMDATA/lnd/clm2/surfdata_map/surfdata_0.25_SCA_hist_78pfts_CMIP6_simyr2000_c190819.nc"
    EOF

If you want to print out only selected variables in hourly resolution in the model output files for each year:

::

    cat << EOF > user_nl_clm
    fsurdat="$MYCTSMDATA/lnd/clm2/surfdata_map/surfdata_0.25_SCA_hist_16pfts_Irrig_CMIP6_simyr2000_c190814.nc"
    hist_empty_htapes = .true.
    hist_fincl1 = 'TSA', 'TSKIN', 'EFLX_LH_TOT', 'FSH', 'WIND', 'TWS', 'SNOWLIQ', 'SNOWICE', 'SNOW_DEPTH', 'TSOI', 'H2OSOI'
    hist_nhtfrq = -1
    hist_mfilt = 365
    EOF
    
**Note that** the variable names (e.g. 'TSA', 'TSKIN', 'EFLX_LH_TOT', etc.) are following the CESM name convention.


Build and submit the case
~~~~~~~~~~~
Final step is to build the case and submit the job to the queue. 

::

    ./case.build
    ./case.submit


