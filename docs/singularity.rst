Singularity / Apptainer
======

Overview
--------

`Apptainer (formerly Singularity) <https://apptainer.org/>`_ is a container runtime designed for HPC clusters and multi-user systems. With a single SIF file (Singularity Image Format), you can run the full OpenSWATH workflow—including OpenMS tools, PyProphet, TRIC (`feature_alignment.py`), DIAlignR, EasyPQP, SAGE, ARYCAL, mapDIA, and Percolator—without installing any dependencies.

This page shows three easy ways to obtain the OpenSWATH image:

* **Option A (recommended):** Download a prebuilt `.sif` from the **GitHub Release assets**.
* **Option B:** Pull directly from the **OpenSWATH Docker image on GHCR** and let Apptainer convert it to SIF locally.
* **Option C:** Pull the prebuilt SIF as an **OCI artifact** from GHCR using ORAS transport (Apptainer ≥ 1.3).

Installing Apptainer
--------

Follow the instructions for your platform: `Install Apptainer <https://apptainer.org/docs/>`_.
On HPC systems, Apptainer is often already installed (e.g., as a module). Ask your system administrator if unsure.

Obtaining the Image
--------

Option A — Download from Release assets

```

1) Go to the OpenSWATH `**GitHub Docker Releases** <https://github.com/OpenSWATH/docker/releases>_` page and download the SIF artifact (e.g., ``openswath-v0.3.0.sif`` or ``openswath-latest.sif``).  
2) Put it somewhere convenient, e.g. your home directory.

Option B — Convert the Docker image to SIF locally
```

Apptainer can convert a public Docker/OCI image to a SIF locally (no root/admin needed):

.. code-block:: bash

# Pull & convert latest image from GitHub Container Registry (GHCR)

apptainer pull openswath-latest.sif 
docker://ghcr.io/openswath/openswath:latest

# Or pull a specific release tag

apptainer pull openswath-v0.3.0.sif 
docker://ghcr.io/openswath/openswath:v0.3.0

Option C — Pull a prebuilt SIF via ORAS (OCI artifact on GHCR)

```

If you’re using **Apptainer ≥ 1.3**, you can pull a prebuilt SIF directly via **ORAS** transport:

.. code-block:: bash

   # From GHCR SIF repository (public)
   apptainer pull openswath-v0.3.0.sif \
       oras://ghcr.io/openswath/openswath-sif:v0.3.0

   # Or the "latest" alias (if provided)
   apptainer pull openswath-latest.sif \
       oras://ghcr.io/openswath/openswath-sif:latest

Running OpenSWATH with Apptainer
--------------------------------

Open a terminal (or your HPC login shell) and bind a host directory (e.g. ``~/Desktop``) into the container as ``/data``:

.. code-block:: bash

   # Start an interactive shell inside the container
   apptainer shell --bind ~/Desktop:/data openswath-latest.sif

Inside the shell, all tools are available on ``$PATH``:

.. code-block:: bash

   # OpenMS
   OpenSwathWorkflow --help

   # PyProphet
   pyprophet --help

   # TRIC
   feature_alignment.py --help

   # DIAlignR
   alignTargetedRuns --help

   # ARYCAL
   arycal --help

   # EasyPQP
   easypqp --help

   # SAGE
   sage --help

   # Percolator
   percolator --help

You can also run single commands without entering a shell:

.. code-block:: bash

   apptainer exec --bind ~/Desktop:/data openswath-latest.sif \
       OpenSwathWorkflow --help

Processing Example
------------------

Assuming your input files are in ``~/Desktop`` and you want outputs written there as well:

.. code-block:: bash

   apptainer exec --bind ~/Desktop:/data openswath-latest.sif \
     OpenSwathWorkflow \
       -in /data/data.mzML \
       -tr /data/library.pqp \
       -tr_irt /data/iRT_assays.TraML \
       -swath_windows_file /data/SWATHwindows_analysis.tsv \
       -sort_swath_maps -batchSize 1000 \
       -readOptions cacheWorkingInMemory -tempDirectory /tmp/ \
       -enable_ms1 true \
       -mz_extraction_window 50 -ppm \
       -mz_correction_function quadratic_regression_delta_ppm \
       -TransitionGroupPicker:background_subtraction original \
       -RTNormalization:alignmentMethod linear \
       -Scoring:stop_report_after_feature 5 \
       -out_features /data/osw_output.osw

.. note::

   - Replace paths under ``/data/`` with your actual filenames on the host.
   - You can bind multiple directories using ``-B /host/dir1:/container/dir1,/host/dir2:/container/dir2``.
   - The SIF is read-only; write outputs to a bound host directory like ``/data``.

Updating the Image
------------------

- **If you used Option A**: Download the new SIF from the latest Release assets and replace your local file.
- **If you used Option B**: Re-run ``apptainer pull`` with the desired tag (e.g., ``latest`` or a specific version).
- **If you used Option C**: Re-run the ``oras://`` pull for the updated tag.

Troubleshooting
---------------

- **Command not found**: Make sure you’re using ``apptainer exec <sif> <command>`` or have started ``apptainer shell``.
- **Permission issues on HPC**: Apptainer is unprivileged, but some clusters require module loading (e.g., ``module load apptainer``). Check with your admin.
- **Cannot write files**: Ensure you used ``--bind <hostdir>:/data`` and write results to ``/data`` in the container.

Software version information
----------------------------

For image contents and version pins, see the `OpenSWATH Docker/Apptainer repository <https://github.com/OpenSWATH/docker>`_. The `**GitHub Docker Releases** <https://github.com/OpenSWATH/docker/releases>_` page lists the corresponding SIF assets.
