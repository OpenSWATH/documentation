Generic Calibration Transition Lists
========================

Overview
--------

OpenSWATH [1]_ supports generic calibration transition lists in tab- or comma-separated value (TSV/CSV) and sqlite-based (PQP) formats. ``TargetedFileConverter`` is part of `OpenMS
<http://www.openms.org>`_ [2]_ and is available for Windows, macOS and Linux.

Contact and Support
-------------------

For ``TargetedFileConverter``, we provide support via the `OpenMS
<http://www.openms.de/support/>`_ support channels.

Spiked-in Biognosys iRT Kit
-------------------------

The Biognosys iRT kit [1]_ is a popular choice for retention time calibration. The kit contains 11 non-naturally occuring synthetic peptides optimized for stability, sensitivity and even retention time spacing over the productive gradient. The iRT peptides can be spiked into the sample and are used to calibrate the retention time of the assay library to the specific chromatographic setup. You can find a copy of the iRT peptide sequences and their corresponding iRT values in `TSV <https://github.com/OpenSWATH/workflows/blob/master/data_library/irtkit.txt>`_ or `TraML <https://github.com/OpenSWATH/workflows/blob/master/data_library/irtkit.TraML>`_

Conserved Eukaryotic Internal Retention Time (CiRT) Peptides
------------------------------------------------------

Parker et al. identified a set of peptide sequences (113) that are conserved across most common eukaryotic species, termed Common internal Retention Time (CiRT) standards [2]_. These are useful peptides to use for retention time calibration if the synthetic spiked-in iRT kit was not used in your experiment. You can find a copy of the CiRT peptide sequences and their corresponding iRT values in `TSV <https://github.com/OpenSWATH/workflows/blob/master/data_library/cirtkit.txt>`_ or `TraML <https://github.com/OpenSWATH/workflows/blob/master/data_library/cirtkit.TraML>`_


References
----------

.. [1] https://biognosys.com/product/irt-kit/
.. [2] Parker Sarah J., Röst Hannes L., Rosenberger George, et al. Identification of a Set of Conserved Eukaryotic Internal Retention Time Standards for Data-independent Acquisition Mass Spectrometry. Molecular and Cellular Proteomics Volume 14, Issue 10, Pages 2800-2813 (2015). doi: https://doi.org/10.1074/mcp.O114.042267