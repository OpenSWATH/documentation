Docker
======

Overview
--------

Docker is a flexible and popular container management platform. With only few steps, you can obtain a full OpenSWATH installation including all dependencies on Windows, macOS and Linux. The OpenSWATH docker container will continuously be updated to the latest development version.

Users who just want to run the OpenSWATH workflow on their desktops can follow the instructions below to obtain an installation-free, always-updated workflow. It will run with nearly native speed on desktops, clusters and cloud environments.

Installing Docker
-----------------

Install `Docker Community Edition <https://www.docker.com/community-edition>`_. for Windows, macOS or Linux. On Linux, most current distributions provide this out-of-the-box. After installation, ensure that you assign the number of CPUs and RAM that you would like to use with OpenSWATH (e.g. 4 CPUs, 8GB RAM).

Running OpenSWATH in Docker
---------------------------

Make sure that Docker is up and running in the background. On macOS or Linux, start a `Terminal` session. On Windows, start PowerShell, CMD or similar. Execute the following steps within the console:

.. code-block:: bash

   # Download OpenSWATH image (grosenberger/openswath:latest)
   docker pull grosenberger/openswath:latest

This will download the latest version of the OpenSWATH Docker image and cache it on your machine.

.. code-block:: bash

   # Generate tutorial container (osw_tutorial) and log in
   docker run --name osw_tutorial --rm -v ~/Desktop/:/data -i -t grosenberger/openswath:latest

This command will start a container based on the OpenSWATH image and map the local volume `~/Desktop/` (from your desktop) to `/data/` (to your container). It will open a Bash command line within the container for you to control the individual components of the workflow. If you want to exit the session, just type `exit` to return to your console. 

Within the running container, you can execute all commands as you would in a native environment:

.. code-block:: bash

   # Execute OpenSwathWorkflow in docker
   OpenSwathWorkflow --help

   # Execute PyProphet in docker
   pyprophet --help

   # Execute TRIC in docker
   feature_alignment.py --help

All data that will be stored in `~/Desktop`, will be available in `/data/`. For example, we can process and write back the files like this:

.. code-block:: bash

    OpenSwathWorkflow \
    -in /data/data.mzML
    -tr /data/library.tsv \
    -tr_irt /data/iRT_assays.TraML \
    -swath_windows_file /data/SWATHwindows_analysis.tsv \
    -sort_swath_maps -batchSize 1000 \
    -readOptions cacheWorkingInMemory -tempDirectory /tmp/ \
    -use_ms1_traces \
    -mz_extraction_window 50 -ppm \
    -mz_correction_function quadratic_regression_delta_ppm \
    -TransitionGroupPicker:background_subtraction original \
    -RTNormalization:alignmentMethod linear \
    -Scoring:stop_report_after_feature 5 \
    -out_tsv /data/osw_output.tsv \
