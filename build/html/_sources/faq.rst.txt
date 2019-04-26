.. _faq:

Common questions and answers
============================


* :ref:`stop_midrun`

* :ref:`pause_run`

* :ref:`restart_when_stopped`

* :ref:`where_diags`

* :ref:`find_failure`


.. _stop_midrun:

How do you stop a simulation once it has started?
-------------------------------------------------

There are a couple of ways to do this depended what your end goal may be.

*  If you want to stop a run to start over:
  
    *  With the gui open, right click on any task that is running and select the kill option.
 
    *  Then select the "Stop suite" button at the top.

    *  When you are ready to restart the simulation, click the "Run" button and select "Cold-start" to start the run up from the beginning.

*  If you want to stop a run to start it again later:
  
    *  With the gui open, right click on any task that is running and select the kill option.
 
    *  Then select the "Stop suite" button at the top.

    *  When you are ready to restart the simulation, click the "Run" button and select "Restart" to continue where it left off.


.. _pause_run:

How do you pause a run in order to evaluate it and start it back up?
--------------------------------------------------------------------

*  With the gui open, select the task you would like to pause at and select the "Hold" option.

*  When you are ready to continue the run, right click on the task and select the "Release" option.


.. _restart_when_stopped:

How do you restart a Cylc suite if it has stopped?
--------------------------------------------------

*  With the gui open, select the 'Run' button.

*  In the window that pops up, select the 'restart' option and then hit 'Start'

*  Do not select the cold-start option in this window unless you want to start the simulation from the beginning.   


.. _where_diags:

Where are my diagnostics copied to?
-----------------------------------

*  Where your diagnostics are copied over to are controlled by the last set of variables in your <case_dir>/postprocess/env_postprocess.xml file

*  By default, the root html link is http://webext.cgd.ucar.edu/.  The rest of the path is controlled by the GLOBAL_REMOTE_WEBDIR variable in the env_postprocess.xml file.  Everything after "/project/diagnostics/external/" will be the remainder of the path. 

*  For example, if you have <entry id="GLOBAL_REMOTE_WEBDIR" value="/project/diagnostics/external/B1850/b.e21.B1850.f09_g17.myCMIPrun/" />, you will find your diagnostics within the directory http://webext.cgd.ucar.edu/B1850/b.e21.B1850.f09_g17.myCMIPrun/
 

.. _find_failure:

Where do I look to find out why something failed?
-------------------------------------------------

*  There are a couple of ways to find out:

    *  Once CESM starts running, you will need to look within the log files within your run directory

    *  If the failure was before CESM started running or the task wasn't running the CESM model:

        *  Open the Cylc gui and right click on the task that failed.  Then select the View menu and select the job stdout and job stderr options

        *  The above will only work if you want to see the last attempt.  If you want to look at previous attempts or want to look at the job status files outside the gui, you can find these files within this path:  /glade/u/home/cmip6/cylc-run/<casename>.suite.cmip6/log/job/1/<task name>/<attempt number>/


