NOTE this is an altered copy of https://github.com/ESCOMP/ctsm/wiki/Protocols-on-updating-FATES-within-CTSM

Updating FATES for your own use 

When a FATES user wants to point to a different version of FATES than CTSM is pointing to. The first list are the steps you do to update your CTSM clone to point to a different NGEET FATES version. 

    1. $EDITOR Externals_CLM.cfg (located in ~/ctsm) 

    2. Change repo_url to the NGEET fork (repo_url = https://github.com/NGEET/fates) 

    3. Change tag = <tagname> to branch = master (or another branch of NGEET/fates) 

    4. ./manage_externals/checkout_externals (will add a remote for fates called NGEET_fates) 

    5. cd src/fates 

    6. git remote rename origin NCAR_fates # Rename origin 

    7. git remote add $GITUSER https://github.com/$GITUSER/fates.git # Add a remote for your fork 
    

Creating a branch on your fork 

These next steps are what you do to create a new FATES branch from your fork, to checkin changes that you are working on. It does assume you did all of the previous steps. This includes the step of creating a new branch, if you already have a branch on your fork, you can skip that step. To create a branch to work on... 

    1. cd ~ctsm/src/fates 

    2. Create branch from NGEET_fates branch or tag: i.e. git checkout -b <newbranch> 

    3. Send the new branch to your fork i.e. git push -u $GITUSER <newbranch> # Note, assumes step 7 above 

    4. cd ../.. (back to ~/ctsm) 

    5. $EDITOR Externals_CLM.cfg 

    6. Change repo_url to your fork (repo_url = https://github.com/$GITUSER/fates) 

    7. Remove tag/branch and add branch keyword for the new branch you made (branch = <newbranch>) 

    8. ./manage_externals/checkout_externals # Fates will now point to your branch as a DETACHED HEAD 

    9. $EDITOR Externals_CLM.cfg 

    10. Now change the repo_url to be your local clone (repo_url = /cluster/home/USERNAME/ctsm/src/fates/) 

    11. ./manage_externals/checkout_externals 

    12. cd src/fates 

    13. git checkout <newbranch> # Do a git checkout of the branch, so you can checkin and no longer in DETACHED HEAD state 

Then make changes and commit to your clone as normal.To push commits on your clone to your fork: 

    14. cd src/fates 

    15. git push -u $GITUSER <newbranch> 
