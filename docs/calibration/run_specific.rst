Run-Specific Calibration Transition Lists
========================

Overview
--------

Run-specific transition lists can be generated from search results of pseudo-DDA data (e.g. from DIA-Umpire or diaTracer) derived from the same DIA raw files. This approach can be used to improve the calibration of retention time, mass-to-charge and ion mobility values for each individual run. 

OpenSWATH [1]_ supports generic calibration transition lists in tab- or comma-separated value (TSV/CSV) and sqlite-based (PQP) formats. ``OpenSwathAssayGenerator`` is part of `OpenMS
<http://www.openms.org>`_ [2]_ and is available for Windows, macOS and Linux.

Contact and Support
-------------------

FragPipe is supported by a very active community via their `github issues tracker
<https://github.com/Nesvilab/FragPipe/issues>`_.

EasyPQP is supported by a very active community via their `github issues tracker
<https://github.com/grosenberger/easypqp/issues>`_.

For ``OpenSwathAssayGenerator``, we provide support via the `OpenMS
<http://www.openms.de/support/>`_ support channels.

Tutorial
--------

The easiest way to generate run-specific transition lists is to use the FragPipe platform. The FragPipe team provides extensive documentation and `tutorials <https://fragpipe.nesvilab.org/docs/tutorial_fragpipe.html>`_ on their website. Please follow their instructions to generate spectral libraries. The resulting ``*_run_peaks.tsv`` files will be in a tab delimited format that can be directly processed with the `OpenSwathAssayGenerator` tool in OpenMS ( follow the remaining steps in the :doc:`../generic` section).

OpenSwathWorkflow allows for an optional additional nonlinear calibration step (performed after linear alignment). You can reduce the ``*_run_peaks.tsv`` to a smaller set of peptides to be used for the linear calibration step by using EasyPQP.

.. code-block:: bash

    run1_diau_run_peaks_file=sample_1_run_peaks.tsv
    OpenSwathAssayGenerator -in $run1_diau_run_peaks_file -out sample_1_run_peaks_irt_nonlinear.pqp
    easypqp reduce --in sample_1_run_peaks_irt_nonlinear.pqp --out sample_1_run_peaks_irt_linear.pqp --bins 10 --peptides 5

