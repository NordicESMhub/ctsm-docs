Setup CTSM
===========

Accounting
----------------

For running CTSM, you usually need to have access to a High-Performance Computer.

If you are working in Norway and at UiO, you can use abel and need to be part of an active account:

- Notur or geofag

To check which project you can use:

:: 

  projects


This will return something like:

::

  geofag
  nn1000k

In the example above, two projects can be used (geofag and nn1000k). Then make sure you choose the right project when running CTSM.



Inputdata
----------

(only first time)

::

    cd ~/ctsm/cime/scripts
    ./link_dirtree $CESM_DATA /work/users/$USER/inputdata


Machines
---------
