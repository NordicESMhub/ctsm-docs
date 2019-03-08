Get CTSM 
=========

Get CTSM for running (no source changes)
-----------------------------------------

If you are a novice, that's where you should start. You get the source code and run it as is.

NordicESMhub maintains a CTSM repository with all the configuration files for running on machines in the Nordics. For now we support:

- abel (UiO, Norway)

If you machine is not in the list and would like we support it, please contact us.

How to get CTSM
++++++++++++++++
CLONE from GitHub (run only first time, or to update clm/ctsm version), in home folder

::

    git clone -b release-clm5.0 https://github.com/NordicESMhub/ctsm.git

LOAD necessary modules in .bashrc (only first time)

::

    emacs .bashrc & 

On abel, add these three modules 

::

    module load python2/2.7.10
    module load cesm
    module load git
		        
save and close

CHECK loaded modules
		 
::

    module list


How to get a specific branch
+++++++++++++++++++++++++++++


Which branch do I run?
++++++++++++++++++++++


Get CTSM for modifying the source code
-----------------------------------------

Create your own branches before continuing, in ~/ctsm AND in ~ctsm/cime. Do this for FATES or any other modules in addition.
    
::

    git branch <username_clm5.0.12>   # useful for remembering version, name according to function e.g. username_cime_clm5.0.12 and username_fates_clm5.0.12
    git checkout  <username_clm5.0.12>
    git branch # to verify that you are on the branch you just created

When you need to do your own development.
