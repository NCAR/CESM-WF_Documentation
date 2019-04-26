.. _instructions_setup:

Instructions on how to setup a CMIP6 experiment
================================================================

**What the workflow script will setup for you:**

  * Creates the CESM case
  * Sets up the full post processing suite
  * Retrieves the CMIP6 experiment information from the experiment database
  * Determines which variables need to be outputted by CESM
  * Sets your CESM and post processing XML variables
  * Creates the input specifications needed to generate the published version of the data
  * Sends the case information to the database for an initial check-in
  * Sets up the Cylc environment for you based on your CESM environment.

**What can the Cylc interface run automatically for you:**

  * All CESM jobs
  * The short term archiver between each CESM job
  * The four main component diagnostic packages
  * The time series generator
  * Create the publishable files
  * Command line scripts

This captures a static system.  If things need to be done mid-run, suite customization is possible with some hand editing of the file suite.rc.  Instructions on how to customize a suite can be found in this section: :ref:`mods`.

**Instructions for running on Cheyenne**

(1) Log into cheyenne with the -Y option

(2) Run the following command and when prompted, use your cheyenne credentials to sudo into the cmip6 service account.  This is a special command created by CISL that you should have available to you and allows you to carry your X display information over to the cmip6 account. 

.. code-block:: bash

    sudox cmip6

(3) For a one time setup,

.. code-block:: bash

    cd
    cd $Your_Cheyenne_Username 
    git clone https://github.com/NCAR/CESM-WF

(4) Enter the experiment into the experiment database

.. _Experiment Database: https://csegweb.cgd.ucar.edu/expdb2.0/cgi-bin/login.cgi
.. _CMIP6 experiment cv: https://github.com/WCRP-CMIP/CMIP6_CVs/blob/master/CMIP6_experiment_id.json

   (a) Log into the `Experiment Database`_.

   (b) Click on “Reserve a CMIP6 Case Name” tab in the blue bar.

   (c) Fill out the form:

       * Provide a unique CESM case name.
   
           **Important Note.**  Remember the case name you use in this step.  It must match identically to what you will use for your --case argument when you setup the experiment.

       * Provide a brief tile and description of the experiment.

       * Indicate if this experiment will be an ensemble and fill in how many members there will be and how long each member will run for.

       *  Assign a person to be the contact person for this experiment (usually who will be running it) and a scientific lead who will log in and trigger the publication of the experiment.

       * Add any case notes to the experiment.  These are handy for reproducing the experiment later on.

       * Select the CMIP experiment name from the pull down menu.

       * Select the CMIP6 parent experiment from the pull down menu.  If there isn't a parent, leave it as the default option.  This page is very helpful in determining if an experiment has a CMIP6 parent experiment `CMIP6 experiment cv`_.  You just need to locate your experiment and look at the "parent_activity_id" and "parent_experiment_id" entries.

       * Enter the branch method.  This should only be 'standard' (which is a CESM hybrid run), 'no parent', or a perturbation explanation.

       * Enter the number of model years to be ran.

       * Enter both the branch time in child (the CESM RUN_STARTDATE value) and the branch time in parent (the CESM RUN_REFDATE).  You can leave the branch time in parent entry blank if the `CMIP6 experiment cv`_ indicates that there is no parent.

       * Fill in the realization, initialization, physics, and forcing indexes.  These are typically all set to 1.  If you need any other values, please contact Sheri or Gary for more directions.

       * Select the model source.  This should be the model that is used for the experiment.

       * Select the source type.  This may or may not match CESM terminology.  For the options that can be selected, you'll need to look up the experiment within the `CMIP6 experiment cv`_.  You'll need to select the "required model components" and you can optionally add anything listed under "additional_allowed_model_components".  These are the only options that can be selected.  If anything else is selected, the files cannot be published to ESGF.

       * Select 'Submit' 

(5) Now back to cheyenne .... 

(6) cd into your CESM-WF directory.

(7) Copy either the create_cylc_newcase or the create_cylc_ensemble script and rename it whatever you would like.

(8) Edit this script to match the experiment you want to run.  Edit the following variables:

   * email: remove os.environ['USER']+ and add in your email after the single quote.  You can leave the other two email addresses in there.

   * WACCM_RUN, HIRES_RUN, CLM_RUN: leave these set to False unless you are running one of these types of experiments.  If you are, set the correct entry to True.

   * cesm_xml: these are CESM variables found within the xml files in the CESM case directory.  The defaults are the main ones that need to be changed for each experiment.  Feel free to add any other xml variables you would like to change.  This script will loop through all of the variables listed and call xml_change to set the variables for you.

   * pp_xml: these are post processing variables that will be set for you.  As with cesm_xml, you can add additional variables if needed.  Some variables can have multiple values if you would like to run the diagnostic packages more than once during a run.  You will need to adhere to this convention example: "1850,1900,1950".  It requires a comma separated list with no spaces.  You can add as many dates as you would like, except you must make sure that you have the same number of dates for all variables for that modeling component.  For example, if 'ATMDIAG_test_first_yr' has 4 dates, 'ATMDIAG_test_nyrs' must also have 4 dates.  The following variables can have lists:

      * ATMDIAG_test_first_yr

      * ATMDIAG_test_nyrs

      * OCNDIAG_YEAR0

      * OCNDIAG_YEAR1

      * OCNDIAG_TSERIES_YEAR0

      * OCNDIAG_TSERIES_YEAR1
     
      * LNDDIAG_clim_first_yr_1

      * LNDDIAG_clim_num_yrs_1

      * LNDDIAG_trends_first_yr_1

      * LNDDIAG_trends_num_yrs_1

      * ICEDIAG_BEGYR_DIFF

      * ICEDIAG_ENDYR_DIFF

      * ICEDIAG_BEGYR_CONT

      * ICEDIAG_ENDYR_CONT

      * ICEDIAG_YRS_TO_AVG

    The last 3 variables in the list control where the diagnostic plots are posted.  You can change this if the format is not what you would like.

   * cesm_code_base: set it to match the top level directory of the CESM code base you would like to use.

(9) Run the script you've just edited with the same commands that you would run with create_newcase.  For example, if I named my edited script 'my_cesm_case', I would run it with a command like:

.. code-block:: bash

    ./my_cesm_case --case /glade/work/cmip6/cases/MyMIP/my_cesm_case --compset B1850 --res f09_g17

(10) Finalize setting up the experiment.  The setup done by the script may only get you part of the way there, depending on the experiment.  This is the time to make any changes that are needed.  This includes adding extra source mods, modifying the user namelist, adding variable output, and so on.  Once this is complete, you will need to run ./case_setup and ./case_build.  If you've made changes to the pe layout and/or the STOP_N, STOP_OPTION, or RESUBMIT variables in env_run.xml, you will have to edit your Cylc suite.rc file in order to submit your jobs correctly.  See the :ref:`mods`  section for these directions.  

     If you're unsure about which variables to output, you can look at the CESM_VARS_NEEDED_* and user_nl_*.fincls files in your CESM case directory for guidance.  Listed in these files are all of the CESM variables you would need to output in order to fulfill the entire request for your particular CMIP6 experiment.  Use caution when selecting high frequency output and only include what you and your group finds important in order for science to be done.  Remember that these lists are just a request and not a demand ...  

(11) Now you're ready to run.  See the next set of instructions on how to run the experiment through the Cylc interface.  :ref:`instructions_run`.
 
