OpenSWATH
=========

Overview
--------

OpenSWATH [1]_ is a proteomics software that allows analysis of LC-MS/MS DIA (data independent acquisition) data using the approach described by Gillet et al. [2]_ and implemented as part of OpenMS [3]_. The original SWATH-MS method uses 32 cycles to iterate through precursor ion windows from 400-426 Da to 1175-1201 Da and at each step acquire a complete, multiplexed fragment ion spectrum of all precursors present in that window. After 32 fragmentations (or 3.2 seconds), the cycle is restarted and the first window (400-426 Da) is fragmented again, thus delivering complete "snapshots" of all fragments of a specific window every 3.2 seconds.

The analysis approach described by Gillet et al. extracts ion traces of specific fragment ions from all MS2 spectra that have the same precursor isolation window, thus generating data that is very similar to SRM traces.

Contact and Support
-------------------

We provide support for OpenSWATH using the `OpenMS support channels
<http://open-ms.sourceforge.net/support/bugs/>`_. Please address general questions to the open-ms-general mailing list.

You can contact the authors `Hannes Röst
<http://www.hroest.ch>`_ and `George Rosenberger
<http://www.rosenberger.pro>`_.

Installation
------------
OpenSWATH is completely integrated into `OpenMS
<http://www.openms.org>`_. We suggest to always use the most current development version of OpenMS to get the newest OpenSWATH developments. Alternatively, the `OpenMS releases
<https://github.com/OpenMS/OpenMS/releases>`_ provide installers for all plattforms.

We recommend to compile a nightly snapshot from the `OpenMS Git repository
<https://github.com/OpenMS/OpenMS>`_ on Linux or, if this is not possible, to download the `nightly builds for Windows
<http://ftp.mi.fu-berlin.de/pub/OpenMS/nightly_binaries/>`_. Installation and compilation instructions can be obtained from the `OpenMS documentation
<http://ftp.mi.fu-berlin.de/pub/OpenMS/release-documentation/html/index.html>`_.

Tutorial
--------
The core step of OpenSWATH conducts signal processing by data extraction from DIA data. It requires mzXML or mzML files. Since 2017, `ProteoWizard
<http://proteowizard.sourceforge.net/>`_ natively supports conversion of SCIEX, Thermo and Waters DIA data. The generated mzXML and mzML files are directly compatible with OpenSWATH. Previous workarounds are not necessary anymore and should be avoided.

Four native interfaces exist to use OpenSWATH:

Integrated Executable
~~~~~~~~~~~~~~~~~~~~~
Since OpenMS 2.0, the executable ``OpenSwathWorkflow`` provides a fast and efficient analysis. Most users will want to directly use this workflow.

An extended tutorial describing a complete OpenSWATH analysis workflow using ``OpenSwathWorkflow`` was recently published [4]_ and is also available from `bioRxiv
<http://biorxiv.org/content/early/2016/03/19/044552>`_.

Classic TOPP Workflow
~~~~~~~~~~~~~~~~~~~~~
The original "classic" workflow provides different OpenMS TOPP [5]_ executables that can also be accessed from different supported workflow managers. The `OpenMS User Tutorial
<http://www.openms.de/tutorials/>`_ provides detailed instructions.

Python interface via pyOpenMS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Many OpenMS classes are also available from within Python. pyOpenMS [6]_ provides access to most functionality with Python data structures. Example scripts for both OpenMS and OpenSWATH can be found in the `OpenMS User Tutorial
<http://www.openms.de/tutorials/>`_.

Illumina BaseSpace
~~~~~~~~~~~~~~~~~~
OpenSWATH is available as native application on Illumina `BaseSpace
<http://www.basespace.com>`_. Together with `SCIEX OneOmics
<https://sciex.com/applications/life-science-research/multi-omics-bioinformatics>`_ it can analyse SWATH-MS data on the Amazon Cloud.

Data
----
Availability
~~~~~~~~~~~~
The SWATH-MS Gold Standard and *Streptococcus pyogenes* data sets are available from the PeptideAtlas raw data repository with accession number `PASS00289
<http://www.peptideatlas.org/PASS/PASS00289>`_.

The Skyline results are available from `Skyline Panorama Webserver
<https://daily.panoramaweb.org/labkey/project/Aebersold/rosenberger/OpenSWATH_SGS/begin.view>`_.

SWATH-MS Gold Standard
~~~~~~~~~~~~~~~~~~~~~~
- 90 mzXML instrument data files
- 90 WIFF raw instrument data files
- SGS TSV assay library
- SGS TraML assay library
- SGS OpenSWATH results
- SGS Skyline results on Panorama
- SGS manual results

Streptococcus pyogenes
~~~~~~~~~~~~~~~~~~~~~~
- 4 mzXML instrument data files
- 4 WIFF raw instrument data files
- *S. pyo* TSV assay library
- *S. pyo* TraML assay library
- *S. pyo* OpenSWATH results
- *S. pyo* summary results

References
----------
.. [1] Röst HL, Rosenberger G, Navarro P, Gillet L, Miladinović SM, Schubert OT, Wolski W, Collins BC, Malmström J, Malmström L, Aebersold R. OpenSWATH enables automated, targeted analysis of data-independent acquisition MS data. Nat Biotechnol. 2014 Mar 10;32(3):219-23. doi: 10.1038/nbt.2841. PMID: 24727770

.. [2] Gillet LC, Navarro P, Tate S, Röst H, Selevsek N, Reiter L, Bonner R, Aebersold R. Targeted data extraction of the MS/MS spectra generated by data-independent acquisition: a new concept for consistent and accurate proteome analysis. Mol Cell Proteomics. 2012 Jun;11(6):O111.016717. Epub 2012 Jan 18. PMID: 22261725

.. [3] Röst HL, Sachsenberg T, Aiche S, Bielow C, Weisser H, Aicheler F, Andreotti S, Ehrlich HC, Gutenbrunner P, Kenar E, Liang X, Nahnsen S, Nilse L, Pfeuffer J, Rosenberger G, Rurik M, Schmitt U, Veit J, Walzer M, Wojnar D, Wolski WE, Schilling O, Choudhary JS, Malmström L, Aebersold R, Reinert K, Kohlbacher O. OpenMS: a flexible open-source software platform for mass spectrometry data analysis. Nat Methods. 2016 Aug 30;13(9):741-8. doi: 10.1038/nmeth.3959. PMID: 27575624

.. [4] Röst HL, Aebersold R, Schubert OT. Automated SWATH Data Analysis Using Targeted Extraction of Ion Chromatograms. Methods Mol Biol. 2017;1550:289-307. doi: 10.1007/978-1-4939-6747-6_20. PMID: 28188537

.. [5] Kohlbacher O, Reinert K, Gröpl C, Lange E, Pfeifer N, Schulz-Trieglaff O, Sturm M. TOPP--the OpenMS proteomics pipeline. Bioinformatics. 2007 Jan 15;23(2):e191-7. PMID: 17237091

.. [6] Röst HL, Schmitt U, Aebersold R, Malmström L. pyOpenMS: a Python-based interface to the OpenMS mass-spectrometry algorithm library. Proteomics. 2014 Jan;14(1):74-7. doi: 10.1002/pmic.201300246. PMID: 24420968
