.. CESM_WF_DOC documentation master file, created by
   sphinx-quickstart on Tue Apr  9 08:18:04 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to the CESM Workflow for CMIP6  documentation
=======================================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   instructions_setup
   instructions_run
   mods
   from_scratch
   faq



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


**Indices and tables**

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

