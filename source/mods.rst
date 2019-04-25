.. _mods:

How to modify a CESM Cylc workflow if you need to make changes
==============================================================

**There are two ways you can modify a CESM Cylc workflow if you need to make changes**

**The first way ... handle it within the CESM case directory**

In your case directory, go ahead and make the changes you need.  For example, change your pe layout, STOP_N, RESUBMIT, and so on.  If you've changed the pe layout, run ./case_setup. 

**Important Note.** You will have to edit the RESUBMIT value.  Because we want Cylc to resubmit your jobs and not the CESM environment, the scripts set RESUBMIT to 0 to ensure that CESM won't resubmit itself.  In order to determine the correct number of iterations that you would like CESM to run, you will have to set RESUBMIT back to the correct value.

After you make your changes, open up the Cylc.Instructions.txt file within your case directory.  Within this file there is a long command to call CESM_Cylc_setup. Copy this command and run it on the command line.  It is then recommended to follow the first step in the Cylc.Instructions.txt by looking at the workflow.png file that was generated.  Confirm that this is correct before starting the experiment.


**The second way ... handle it within the Cylc environment**

If you need to update the pe count or add extra tasks that aren't visible in a static view of the CESM environment (ie, adding an extra 5 years to a run with a STOP_N set to 10) this is the preferred method.

First you will need to locate your suite file.  The easiest way to do this is to go into your case directory and look at the file Cylc.Instructions.txt.  The first step to take asks for you to look at workflow.png and contains the path to this file.  cd into the directory where this file is found.  If you're running a CMIP6 experiment under username cmip6, the path is likely to be /glade/u/home/cmip6/cylc_suites/$CASENAME.  If you're running an ensemble, the directoy may also contain the member numbers.

Once in this directory, you will find the 'suite.rc' file associated with this experiment.  This file contains all of the instructions needed to run your entire workflow.  Beofre editing this file, it's recommended you copy the original in case you need to reference it after you make your changes.  

Open the suite.rc file and make the edits you would like (see below for more details).

After any changes to this file, you will need to follow the instructions found in the :ref:`updating_suites` section.


**More on hand editing Cylc suite.rc files**

* :ref:`format_of_file`

* :ref:`updating_suites`

* :ref:`modifying_pe_count`


.. _format_of_file:

Format of the suite.rc File
---------------------------

* The file is formatted in jinja2 templating to avoid redundency within the file.  

* The top section list the date arrays.  If you need to extend your run or change any dates, you will do that here.

* Right after the dates, are the email addresses where job staus updates will be sent to.  

* Next is the dependency graph that deterines when each task will run.  If you've added dates into the date arrays, you will have to enter them into the dependency graph as well.  To do this, follow the same format of task1 => task2, meaning run task1 and if successful, run task2.  If you make any changes here, it is recommended that once you save the suite.rc file, you run the below command to make sure that the graph looks correct and there are no "floating" or unconnected tasks. 

.. code-block:: bash

    cylc graph <your casename>.suite.cmip6 


* The final section lists the individual types of tasks and how to run them.  This is were you can change pe counts, queues, or anything else that has to do with submitting the jobs.  The task types are listed in the double brackets, for example "[[case_run_{{dates_case_run[i]}} ]]".


.. _updating_suites:

Updating Suite Definitions
--------------------------

Once you complete your edits, save the file.  Then you will do one of two things:

*  If you haven't opened the Cylc gui yet, issue the following command on the command line.  The <your casename> should match the name given in the gcylc command listed in the Cylc.Instructions.txt file.

.. code-block:: bash

    cylc register <your casename>.suite.cmip6 /the/directory/to/your/suite.rc/file/

* If you've started the gui and or the above command fails because it says that this Cylc suite has been started, open the suite in the Cylc gui.  Then select the "Control" menuand then select "Reload Suite Definition".  After you do this, you will see a blue bar that moves in the corner.  If you've added or removed tasks, sometimes you need to close the gui and then reopen it.  You will notice that any new tasks you've added are outlined in a gray color.  In this case, right click on the task and choose the "Insert" option.  A box will pop up asking for confirmation for the addition.  This box allows wild cards, so if you are adding several tasks with the same name, you can remove, for example, the date portion of the task anem string and replace it with a '*'.  If you've just changed a node count or the name of a script that is called, no further action is needed after you select the "Reload Suite Definition" option.


.. _modifying_pe_count:

Modifying PE Count
------------------

Open the correct suite.rc file for editing and go to the final section and locate the section for the type of task you need to change.  For example, "[[case_run_{{dates_case_run[i]}} ]]".  Then modify the "-l = select=......" line to match the top of your .case.run file in your case directory.

After this change, you will need to follow the instructions found in the :ref:`updating_suites` section.



