Run CTSM 
========

NB! this example is connected to project nn2806k (for your own project, change the project code. To see available projects and resources, use cost -p):

::

	export CESM_ACCOUNT=nn2806k

Run your very first CTSM case
------------------------------

LOAD externals of CTSM (FATES and so on; only necessary first time), in folder ~/ctsm. If you are updating FATES go here first: (https://github.com/NordicESMhub/ctsm-dev/blob/master/Updating_FATES.md)

::

    ./manage_externals/checkout_externals
        

navigate to ~/ctsm/cime/scripts/


Inputdata
----------

(only first time or whenever it disappears in your workdir i.e. 45 days)

::

    cd ~/ctsm/cime/scripts
    ./link_dirtree $CESM_DATA /work/users/$USER/inputdata


Make a case
-------------

::

   ./create_newcase --case ~/cases/I2000Clm50BgcCruGs  --compset I2000Clm50BgcCruGs   --res f19_g17 --machine abel --run-unsupported --project $CESM_ACCOUNT


navigate to ~/cases/I2000Clm50BgcCruGs


1) check the configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

  ./xmlquery --l #(--l list --f file) 

eg 

::

    ./xmlquery STOP_OPTION
		
2) Change configuration
~~~~~~~~~~~~~~~~~~~~~~~~

::

    ./xmlchange STOP_OPTION=ndays #(nyears, nmonths)
    ./xmlchange STOP_N=5 #(then 5 days)
    ./xmlchange NTASKS=1 #(number of CPU's, can be increased if excitation error)
	
or edit the xml files is another way to change these parameters

3) setup case
~~~~~~~~~~~~~~

::

    ./case.setup  #(--reset)
        
4) edit user_nl_clm   
~~~~~~~~~~~~~~~~~~~~

add this below

::

	hist_mfilt=5 #(number of output files)
	hist_nhtfrq=-24 #(means daily outputs)


`hist_mfilt` allows you to specify the number of output files and `hist_nhtfrq` the frequency; here `-24` means daily outputs.
	
5) case build
~~~~~~~~~~~~~~

::    

    (./case.build --clean)
    ./case.build

6) run case
~~~~~~~~~~~~~~

::

    ./case.submit 

	
Run fates
-----------

NB! Fates is not automatically checked out with the latest version (as it is still under development), and this has to be done manually. 

Follow https://github.com/NordicESMhub/ctsm-dev/blob/master/Updating_FATES (based on https://github.uio.no/huit/clm5.0_notes/issues/26 and https://github.com/ESCOMP/ctsm/wiki/Protocols-on-updating-FATES-within-CTSM)

::

   ./create_newcase --case ../../../ctsm_cases/fates_f19_g17 --compset 2000_DATM%GSWP3v1_CLM50%FATES_SICE_SOCN_MOSART_SGLC_SWAV --res f19_g17 --machine abel --run-unsupported --project $CESM_ACCOUNT
    
Run a single cell case  
-----------------------

Out of the box
+++++++++++++++

::

  ./create_newcase --case ~/cases/brazil_I1PtClm50SpGs --compset I1PtClm50SpGs  --res  1x1_brazil --machine abel --run-unsupported --project geofag

Customized
+++++++++++

::

   ./create_newcase --case ~/cases/brazil_I2000Clm50BgcCruGs --compset I2000Clm50BgcCruGs  --res  1x1_brazil --machine abel --run-unsupported --project geofag

- Step-1: Tutorial_creating_surface_data_for_single_point.md
- Step-2: For atmospheric forcing README_Single point (own atm forcing and surface data) (include scripts)
