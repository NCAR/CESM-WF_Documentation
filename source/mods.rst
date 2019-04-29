.. _mods:

How to modify a CESM Cylc workflow if you need to make changes
==============================================================

**There are two ways you can modify a CESM Cylc workflow if you need to make changes**

**The first way ... handle it within the CESM case directory**

In your case directory, go ahead and make the changes you need.  For example, change your pe layout, STOP_N, RESUBMIT, and so on.  If you've changed the pe layout, run ./case_setup. 

**Important Note.** You will have to edit the RESUBMIT value.  Because we want Cylc to resubmit your jobs and not the CESM environment, the scripts set RESUBMIT to 0 to ensure that CESM won't resubmit itself.  In order to determine the correct number of iterations that you would like CESM to run, you will have to set RESUBMIT back to the correct value.

After you make your changes, open up the Cylc.Instructions.txt file within your case directory.  Within this file there is a long command to call CESM_Cylc_setup. Copy this command and run it on the command line.  It is then recommended to follow the first step in the Cylc.Instructions.txt by looking at the workflow.png file that was generated.  Confirm that this is correct before starting the experiment.


**The second way ... handle it within the Cylc environment**

If you need to update the pe count or add extra tasks that aren't visible in a static view of the CESM environment (i.e., adding an extra 5 years to a run with a STOP_N set to 10) this is the preferred method.

First you will need to locate your suite file.  The easiest way to do this is to go into your case directory and look at the file Cylc.Instructions.txt.  The first step to take asks for you to look at workflow.png and contains the path to this file.  cd into the directory where this file is found.  If you're running a CMIP6 experiment under username cmip6, the path is likely to be /glade/u/home/cmip6/cylc_suites/$CASENAME.  If you're running an ensemble, the directory may also contain the member numbers.

Once in this directory, you will find the 'suite.rc' file associated with this experiment.  This file contains all of the instructions needed to run your entire workflow.  Before editing this file, it's recommended you copy the original in case you need to reference it after you make your changes.  

Open the suite.rc file and make the edits you would like (see below for more details).

After any changes to this file, you will need to follow the instructions found in the :ref:`updating_suites` section.


**More on hand editing Cylc suite.rc files**

* :ref:`format_of_file`

* :ref:`updating_suites`

* :ref:`modifying_pe_count`

* :ref:`add_more_diags`

* :ref:`add_sim_length`

.. _format_of_file:

Format of the suite.rc File
---------------------------

* The file is formatted in jinja2 templating to avoid redundancy within the file.  

* The top section lists the date arrays.  If you need to extend your run or change any dates, you will do that here.

* Right after the dates, are the email addresses where job status updates will be sent to.  

* Next is the dependency graph that determines when each task will run.  If you've added dates into the date arrays, you will have to enter them into the dependency graph as well.  To do this, follow the same format of task1 => task2, meaning run task1 and if successful, run task2.  If you make any changes here, it is recommended that once you save the suite.rc file, you run the below command to make sure that the graph looks correct and there are no "floating" or unconnected tasks. 

.. code-block:: bash

    cylc graph <your casename>.suite.cmip6 


* The final section lists the individual types of tasks and how to run them.  This is where you can change pe counts, queues, or anything else that has to do with submitting the jobs.  The task types are listed in the double brackets, for example "[[case_run_{{dates_case_run[i]}} ]]".


.. _updating_suites:

Updating Suite Definitions
--------------------------

Once you complete your edits, save the file.  Then you will do one of two things:

*  If you haven't opened the Cylc gui yet, issue the following command on the command line.  The <your casename> should match the name given in the gcylc command listed in the Cylc.Instructions.txt file.

.. code-block:: bash

    cylc register <your casename>.suite.cmip6 /the/directory/to/your/suite.rc/file/

* If you've started the gui and or the above command fails because it says that this Cylc suite has been started, open the suite in the Cylc gui.  Then select the "Control" menu and then select "Reload Suite Definition".  After you do this, you will see a blue bar that moves in the corner.  If you've added or removed tasks, sometimes you need to close the gui and then reopen it.  You will notice that any new tasks you've added are outlined in a gray color.  In this case, right click on the task and choose the "Insert" option.  A box will pop up asking for confirmation for the addition.  This box allows wild cards, so if you are adding several tasks with the same name, you can remove, for example, the date portion of the task name string and replace it with a '*'.  If you've just changed a node count or the name of a script that is called, no further action is needed after you select the "Reload Suite Definition" option.


.. _modifying_pe_count:

Modifying PE Count
------------------

Open the correct suite.rc file for editing and go to the final section and locate the section for the type of task you need to change.  For example, "[[case_run_{{dates_case_run[i]}} ]]".  Then modify the "-l = select=......" line to match the top of your .case.run file in your case directory.

After this change, you will need to follow the instructions found in the :ref:`updating_suites`  section.


.. _add_more_diags:

Adding More Diagnostics
-----------------------

The first step is to add the dates for the diagnostics and their settings in the top portion of the suite.rc file.

When you add diagnostics it's important to add to all of the variables for that component.  The groups of variables include the following:

*  ATM diagnostics

    * dates_atm_averages - add dates

    * dates_atm_diagnostics - add dates

    * ATMDIAG_test_first_yr - matches the xml variable

    * ATMDIAG_test_nyrs - matches the xml variable

*  OCN diagnostics

    * dates_ocn_averages - add dates

    * dates_ocn_diagnostics - add dates

    * OCNDIAG_YEAR0 - matches the xml variable

    * OCNDIAG_YEAR1 - matches the xml variable

    * OCNDIAG_TSERIES_YEAR0 - matches the xml variable

    * OCNDIAG_TSERIES_YEAR1 - matches the xml variable

* LND diagnostics

    * dates_lnd_averages - add dates

    * dates_lnd_diagnostics - add dates

    * LNDDIAG_clim_first_yr_1 - matches the xml variable

    * LNDDIAG_trends_first_yr_1 - matches the xml variable

    * LNDDIAG_clim_num_yrs_1 - matches the xml variable

    * LNDDIAG_trends_num_yrs_1 - matches the xml variable

* ICE diagnostics

    * dates_ice_averages - add dates

    * dates_ice_diagnostics - add dates

    * ICEDIAG_BEGYR_DIFF - matches the xml variable

    * ICEDIAG_ENDYR_DIFF - matches the xml variable

    * ICEDIAG_BEGYR_CONT - matches the xml variable

    * ICEDIAG_ENDYR_CONT - matches the xml variable

    * ICEDIAG_YRS_TO_AVG  - matches the xml variable

For each group of variables, make sure you add the same number of entries to each.  Otherwise this will result in an error.

The date variables in each group take a comma separated list.  For example, {% set dates_atm_averages = ['0005-01-01','0010-01-01'] %}.

The DIAG variables also allow for comma separated lists.  For example, {% set ATMDIAG_test_first_yr = [1,5] %}. 

Adding multiple entries allows the diagnostics to be ran more than once during the simulation.  

After you add the diagnostic entries to the lists, they need to be added to the dependency graph so Cylc know when to run them.  This is done in the [[dependencies]] section.  In order to insert the diagnostics, you can follow a similar syntax to what is shown here.  

.. code-block:: bash
   :linenos:

                 case_st_archive_0005-01-01 => atm_averages_0005-01-01 & ocn_averages_0005-01-01 & lnd_averages_0005-01-01 & ice_averages_0005-01-01 & case_run_0007-01-01
                 atm_averages_0005-01-01 => atm_diagnostics_0005-01-01 => atm_diagnostics_0005-01-01_post
                 ocn_averages_0005-01-01 => ocn_diagnostics_0005-01-01 => ocn_diagnostics_0005-01-01_post
                 lnd_averages_0005-01-01 => lnd_diagnostics_0005-01-01 => lnd_diagnostics_0005-01-01_post
                 ice_averages_0005-01-01 => ice_diagnostics_0005-01-01 => ice_diagnostics_0005-01-01_post

After you have added the diagnostics into the dependency graph, save your file and run the following command:

.. code-block:: bash

    cylc graph <your casename>.suite.cmip6 

This will open up a window and display your new workflow graph.  Make sure you don't have any 'float'/non-connected tasks and everything looks as you would anticipate.  

Once everything looks okay, open up the gui and select the Control->Reload suite definition option.  You may have to close and reopen the gui if the new workflow doesn't load. 

Once the tasks have been added, you will need to right click on them and choose 'Insert' to add them in.  Once you make the selection, a window will pop up with the task listed in the box.  If you have to add multiple tasks, you can replace part of the string with wild card characters to make this task easier.  Once added, the task will change from a gray outline to a blue outline.


.. _add_sim_length:

Adding More Years to a Simulation
---------------------------------

Adding extra years to a simulation is similar to adding extra diagnostics.  

The first step is to add year references to both the "dates_case_run" and "dates_case_st_archive" arrays at the top of the suite.rc file.

Next you will have to add these new dates to the dependency graph, in a similar way to what is shown here.

EXISTING CODE:

.. code-block:: bash
   :linenos:

                 case_st_archive_0011-01-01 => timeseriesL_0011-01-01
                 timeseriesL_0011-01-01 => xconform_0011-01-01

MODIFED CODE:

.. code-block:: bash
   :linenos:

                 case_st_archive_0011-01-01 => case_run_0013-01-01
                 case_run_0013-01-01 => case_st_archive_0013-01-01
                 case_st_archive_0013-01-01 => timeseriesL_0011-01-01
                 timeseriesL_0011-01-01 => xconform_0011-01-01

For simplicity, you can leave the dates on the timeseries and xcfonform tasks the same.  It should still run okay as long as the last case_st_archive task points to the timeseries task.

If you would like for the new CESM run calls to run just **like the others**, you do not have to make any other modifications to this file.

If you would like to run the new CESM run calls **differently**, for example, run with a different **STOP_N** value, you will need to add the code that is shown below.

.. code-block:: bash
   :linenos:

         [[case_run_0013]]
         script = cd /gpfs/fs1/work/cmip6/cases/DECK/helloworld; ./xmlchange STOP_N=3; /gpfs/fs1/work/cmip6/cases/DECK/helloworld/case.run.cylc
         [[[job]]]
                 method = pbs
                 execution time limit = PT12H
                 execution retry delays = PT30S, PT120S, PT600S
         [[[directives]]]
                 -A = ACCT00099
                 -q = regular
                 -N = helloworld.run
                 -r = n
                 -j = oe
                 -S = /bin/bash
                 -l = select=141:ncpus=36:mpiprocs=12:ompthreads=3
         [[[event hooks]]]
                 started handler = cylc email-suite
                 succeeded handler = cylc email-suite
                 failed handler = cylc email-suite

It is best to go into the last section of the file and find the line similar to "[[case_run_{{dates_case_run[i]}} ]]".  This line is slightly different if you're running an ensemble.  Copy this entire section, minus the "for" and "endfor" lines.

Then change the "[[case_run_{{dates_case_run[i]}} ]]" in the copied section to match the exact year you will need that change.  In the above example, it was change to be "case_run_0013".

The last change is to the script line.  You'll notice that line 2 contains "./xmlchange STOP_N=3".  This is will be executed before case.run.cylc is called in order to change the value of STOP_N in your case directory.  You will have to add a similar call in your script line to match what needs to be changed between CESM run calls.

After you have finished editing your suite.rc file, save your file and run the following command:

.. code-block:: bash

    cylc graph <your casename>.suite.cmip6 

This will open up a window and display your new workflow graph.  Make sure you don't have any 'float'/non-connected tasks and everything looks as you would anticipate.

Once everything looks okay, open up the gui and select the Control->Reload suite definition option.  You may have to close and reopen the gui if the new workflow doesn't load.

Once the tasks have been added, you will need to right click on them and choose 'Insert' to add them in.  Once you make the selection, a window will pop up with the task listed in the box.  If you have to add multiple tasks, you can replace part of the string with wild card characters to make this task easier.  Once added, the task will change from a gray outline to a blue outline.

