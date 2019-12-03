.. _instructions_pyconform:

Instructions on how to setup and run PyConform independent of the workflow
==========================================================================

**You will need to enter your experiment into the experiment database before proceeding with the below instructions.**

**The database is found at https://csegweb.cgd.ucar.edu/expdb2.0**

**One Time Setup**  
------------------

(1) Run the below command

.. code-block:: bash

    pip install -i https://pypi.python.org/pypi --user  dreqPy==01.00.31

(2) Create a file called ~/.subversion/cmip6.conf and add the following

.. code-block:: bash

    [svn]
    password: your_svn_password

Then run

.. code-block:: bash

    chmod 600 ~/.subversion/cmip6.conf

**Instructions for Running PyConform on Cheyenne**
--------------------------------------------------

(1) Setup the post-processing environment variables:

.. code-block:: bash

    setenv POSTPROCESS_PATH /glade/u/home/mickelso/CESM_postprocessing_3/
    setenv POSTPROCESS_PATH_GEYSER /glade/u/home/mickelso/CESM_postprocessing_3/

(2) Make sure you have the following modules loaded

.. code-block:: bash

    intel/17.0.1
    python/2.7.16
    netcdf/4.6.3
    mpt/2.19

(3) cd into your case directory

(4) Run the following commands

.. code-block:: bash

    alias cesm_pp_activate 'source $POSTPROCESS_PATH/cesm-env2/bin/activate.csh'
    cesm_pp_activate
    create_postprocess --caseroot [fully-qualified-path-to-caseroot]
    ./archive_metadata --query_cmip6 [casename that matches name in database]  db.json --user [your-username] --password --expType CMIP6

(5) Take a look at the $CASE/archive_files/db.json file to verify the information is correct.

(6) cd into the $CASE/postprocess directory that was created in the above set.
 
(7) Open the file env_postprocess.xml and edit the following XML variables:

.. code-block:: bash

    CASEROOT                   (the full path to your case directory)
    PP_CASE_PATH               (your_case_directory/postprocess)
    CASE                       (your case name)
    DOUT_S_ROOT                (the full path to your raw CESM output, usually in the scratch/username/archive directory)
    TIMESERIES_OUTPUT_ROOTDIR  (full path to the timeseries output)

(8) Open the file env_conform.xml and edit the following XML variables:

.. code-block:: bash

    CONFORM_CESM_DEFINITIONS   (modify on if running on a land only or WACCM experiment) 
    CONFORM_EXP_NAM            (the official and exact cmip6 experiment name, see the database for this name)
    CONFORM_JSON_DIRECTORY     (this should be something similar to your_case_directory/postprocess/)
    CONFORM_OUTPUT_DIR         (where you want pyconform to put the output)

(9) Run the command 

.. code-block:: bash

    ./iconform

on the command line from the postprocess directory.  This should create a directory in your postprocessing directory called PyConform_input.  make sure there are files in this directory and make sure your logs/iconform.<date> file does not contain errors that the top of this file.  Warning should be okay.

(10) If the above step passes the two checks, open the xconform file and make sure the pbs settings at the top look okay.

(11) Run this command to submit the job to the queue:

.. code-block:: bash

    qsub xconform

(12) Your output should appear in the directory you set the variable CONFORM_OUTPUT_DIR to.

(13) If no output is produced, check the logs/xconform.<date> for error messages.  PyConform can be difficult to debug because of its internal graph structures and its ability to catch certain errors accurately.  If you run into issues, you can email mickelso.at.ucar.edu with your error information.


