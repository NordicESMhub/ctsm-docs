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

- For instance, to change the duration of a simulation to 5 days:

::

    ./xmlchange STOP_OPTION=ndays #(nyears, nmonths)
    ./xmlchange STOP_N=5 #(then 5 days)
	
or edit the xml files is another way to change these parameters (**not recommended**).

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

    ./case.build


**Remark**: if your build fails or if you make changes and need to rebuild, make sure you clean the previous build:


::    

    ./case.build --clean


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

CLM supports running using single-point or regional datasets that are customized to a particular region. 

In the section below we show you how to run ready to use single-point configurations (out of the box) and then show you how to create your own dataset for any location of your choice.

Out of the box
~~~~~~~~~~~~~~

To run for the Brazil test site do the following:

::

  export CESM_ACCOUNT=nn2806k

  ./create_newcase -case ~/cases/testSPDATASET -res 1x1_brazil -compset I2000Clm50SpGs  --machine abel --run-unsupported --project $CESM_ACCOUNT


**Remark**: make sure you set **CESM_ACCOUNT** to your project.
Customized
~~~~~~~~~~~

- Step-1: 

.. include:: Tutorial_creating_surface_data_for_single_point.rst

- Step-2: For atmospheric forcing (own atm forcing and surface data) (include scripts)

.. include:: README_Single_point.rst 


Change configuration
+++++++++++++++++++++

::

    ./xmlchange NTASKS=1 #(number of CPU's, can be increased if excitation error)



Run a regional case 
-----------------------

.. include:: regional_case.rst
