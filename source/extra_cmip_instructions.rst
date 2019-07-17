.. _extra_cmip_instructions:

Extra Instructions for CMIP6 Experiments
========================================

* :ref:`db`

* :ref:`cmip_files`

* :ref:`ocn_regrid`

.. _db:

Double checking the database to make sure everything is correct
---------------------------------------------------------------

It is very important that you double check to make sure everything for your experiment, and if applicable, each ensemble member is correct within the experiment database.  This information is published into the global attributes for each file we publish.  If any information is incorrect, it will be rejected by the publication software or will require us to retract data and republish it with the correct informaiton.

To correct mistakes, go to the entry for that experiment in the database.  Then select the 'CMIP6 Project Details' tab.  Then select the pencil icon on the 'CMIP File Global Atrributes' column. Make the changes that are needed and then save the results.

Some common mistakes include:

* source_id

    * Make sure that the correct model is selected.

* branch_time_in_parent

    * This should match the time of RUN_REFDATE.  This value is then set to "days since 0001-01-01".  This should be equal to (year-1)*365.

* branch_time_in_child

    * This should match the time of RUN_STARTDATE.  This value is then set to "days since 0001-01-01".  This should be equal to (year-1)*365.

* branch_method

    * If the experiment is listed as having a parent, this should be set to 'standard' if you are running a hybrid run.  If you are adding a pertlim, instead of standard, you will add information in regards to pertlim value selected.

* parent_variant_label

    * Make sure the number after the "r"  matches the ensemble member number of the parent.

* All other fields
    
.. _CMIP6 experiment cv: https://github.com/WCRP-CMIP/CMIP6_CVs/blob/master/CMIP6_experiment_id.json

    * Make sure you follow what needs to be entered in for your experiment on the `CMIP6 experiment cv`_.

If changes are made, you will need to update your case with these changes.  This can be done in these steps:

.. code-block:: bash

    cd $CASEDIR
    ./archive_metadata --query_cmip6 $case_name  db.json --user cmip6 --password --expType CMIP6
    cd postprocess
    ./iconform

These changes can be made and corrected in the case anytime before the last tool (xconform) is ran.  If xconform has already ran, you can run all of the above steps and then you must run

.. code-block:: bash

    qsub xconform

as a final step to regenerate the final files correctly.


.. _cmip_files: 

Double checking the CMIP6 files that will be published
------------------------------------------------------

First you'll need to find out where your CMIP6 files are located.  In order to find the ocrrect directory, run this command:

.. code-block:: bash

   ./pp_config --get CONFORM_OUTPUT_DIR

Next cd into this directory that is outputted.

Once in the numeric cmip6 output directory, run these commands to remove any temporary files that were not able to be computed.  

.. code-block:: bash

   ls */*/*/*/*/*/*/*/*/*/*.tmp.nc
   rm -f */*/*/*/*/*/*/*/*/*/*.tmp.nc

It is not uncommon to see these files because certain variables have not been finalized yet.

**Once the temp files are removed it's important to check the dates on the files to make sure that your simulation finished running to the correct length.  If not, you'll have to move the cmip6 and timeseries files directories to "back/old" directories, finish the simulation to the correct length, and then either run the postprocessing tools by hand or exectute these through Cylc.**


.. _ocn_regrid:

Regridding ocean output files
----------------------------- 

If you've ran a case where you've produced ocean output files you will have to regrid the CMIIP6 files. This is currently being added to the post-processing step and will be made available in the near future.  In the mean time, please contact Sheri for more information about this step. 





