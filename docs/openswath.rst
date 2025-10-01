OpenSWATH
=========

Overview
--------
OpenSWATH [1]_ is a proteomics software that allows analysis of LC-MS/MS DIA (data independent acquisition) data using the approach described by Gillet et al. [2]_, as well as diaPASEF as described by Florian et al. [3]_ and implemented as part of OpenMS [4]_. The original SWATH-MS method uses 32 cycles to iterate through precursor ion windows from 400-426 Da to 1175-1201 Da and at each step acquire a complete, multiplexed fragment ion spectrum of all precursors present in that window. After 32 fragmentations (or 3.2 seconds), the cycle is restarted and the first window (400-426 Da) is fragmented again, thus delivering complete "snapshots" of all fragments of a specific window every 3.2 seconds.

The analysis approach described by Gillet et al. extracts ion traces of specific fragment ions from all MS2 spectra that have the same precursor isolation window, thus generating data that is very similar to SRM traces.

The OpenSwathWorkflow executable is currently the most efficient way of running
OpenSWATH [1]_ [2]_ and it is available through OpenMS [4]_.  An extended
tutorial describing a complete OpenSWATH analysis workflow using
``OpenSwathWorkflow`` was recently published [5]_ and is also available from
`bioRxiv <http://biorxiv.org/content/early/2016/03/19/044552>`_ with its
associated `dataset <http://www.peptideatlas.org/PASS/PASS00779>`_. 

The OpenSwathWorkflow implements the OpenSWATH analysis workflow as described
in [1]_ and provides a complete, integrated analysis tool without the need to 
run multiple tools consecutively. The OpenSwathWorkflow was extended to handle the ion mobility dimension for diaPASEF data as described in [3]_.

It executes the following steps in order:

 - Reading of the raw input file (provided as mzML, mzXML or sqMass) and RT normalization transition list
 - Computing the retention time transformation using RT normalization peptides
 - Reading of the transition list
 - Extracting the specified transitions
 - Scoring the peak groups in the extracted ion chromatograms (XIC)
 - Reporting the peak groups and the chromatograms

Contact and Support
-------------------

We provide support for OpenSWATH using the `OpenMS github issue tracker
<https://github.com/OpenMS/OpenMS/issues>`_. Please address general questions to the `OpenMS discord server <https://discord.gg/4TAGhqJ7s5>`_.

You can contact the authors `Hannes Röst
<http://www.hroest.ch>`_ and `George Rosenberger
<http://www.rosenberger.pro>`_.

Input
-----

The input to OpenSwathWorkflow are provided using the following files:

- ``in`` raw input file (provided as mzML, mzXML or sqMass) 
- ``tr`` transition list (spectral library)
- ``tr_irt`` an optional transition file containing RT normalization coordinates
- ``swath_windows_file`` an optional file specifying the *analysis* SWATH windows

Mass spectrometric data
~~~~~~~~~~~~~~~~~~~~~~~

The input file ``in`` is generally a single ``mzML``, ``mzXML`` or ``sqMass`` file
(converted from a raw vendor file format using `ProteoWizard
<http://proteowizard.sourceforge.net/>`_).

.. note:: If your data has been acquired using a staggered SWATH acquisition scheme, you will need to add a filter in msconvert to **demultiplex** the data with optimization set to **Overlap Only**. 

.. note:: If your data was acquired on a timsTOF for **diaPASEF** data, then you need to use the ``diapysef converttdftomzml`` python tool to convert the raw data to mzML format. See the :doc:`mobidik/dataconversion` section for more details.

Spectral library
~~~~~~~~~~~~~~~~

The spectral library ``tr`` is a spectral library either in ``.tsv``,
``.TraML`` or ``.PQP`` format (where the ``TSV`` or ``PQP`` format is recommended). Further information in generating these files can be found in the :doc:`generic` section.

Retention time normalization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The retention time normalization peptides are provided using the optional
parameter ``tr_irt`` in TraML format. We suggest to use the ``iRTassays.TraML`` file provided in
the tutorial dataset, if the Biognosys iRT-kit was used during sample preparation.

If the iRT-kit was not used, it is highly recommended to use or generate a set of endogenous peptides for RT normalization. A recent publication [5]_ provides such a set of ``CiRT`` peptides suitable for many eukaryotic samples. The TraML file from the supplementary information can be used as input for ``tr_irt``. Since not all ``CiRT`` peptides might be found, the flag ``RTNormalization:estimateBestPeptides`` should be set to improve initial filtering of poor signals. Further parameters for optimization can be found when invoking ``OpenSwathWorkflow --helphelp`` under the ``RTNormalization`` section. Those do not require adjustment for most common sample types and LC-MS/MS setups, but might be useful to tweak for specific scenarios.

SWATH windows definition
~~~~~~~~~~~~~~~~~~~~~~~~

The SWATH windows themselves can either be read from the input files, but it is
recommended to provide them explicitly in tab-delimited form. Note that there is
a difference between the `SWATH window acquisition scheme settings <ftp://PASS00779:SWATH@ftp.peptideatlas.org/SWATHwindows_acquisition.tsv>`_
and the `SWATH window analysis settings <ftp://PASS00779:SWATH@ftp.peptideatlas.org/SWATHwindows_analysis.tsv>`_:

The *acquisition* settings tell the instrument how to acquire the data and how to filter the transitions (see section :doc:`pqp`).

The *analysis* settings on the other hand specify from which precursor isolation windows to extract the data. Note that the analysis windows should not have any overlap.

We suggest to use the ``SWATHwindows_analysis.tsv`` file provided in the tutorial dataset for 32 windows of 25 Da each.

Parameters
----------

Caching of mass spectrometric data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Due to the large size of the files, OpenSwathWorkflow implements a caching
strategy where files are cached to disk and then read into memory
SWATH-by-SWATH. You can enable this by setting ``-readOptions
cacheWorkingInMemory -tempDirectory /tmp`` where you would need to adjust the
temporary directory depending on your platform.

Other potentially useful options you may want to turn on are ``batchSize`` and
``sort_swath_maps``. 

Chromatographic parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~

The current parameters are optimized for 2 hour gradients on SCIEX 5600 /
6600 TripleTOF instruments with a peak width of around 30 seconds using iRT
peptides.  If your chromatography differs, please consider adjusting
``-Scoring:TransitionGroupPicker:min_peak_width`` to allow for smaller or larger
peaks and adjust the ``-rt_extraction_window`` to use a different extraction
window for the retention time. 

Mass spectrometric parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In *m/z* domain, consider adjusting ``-mz_extraction_window`` to your instrument resolution, which can be in Th or
ppm (using ``-ppm``). In addition to using the iRT peptides for correction of
the retention time space, OpenSWATH can also use those peptides to correct the *m/z* space
with the option ``-mz_correction_function quadratic_regression_delta_ppm``. For
quantification, it can be beneficial to enable background subtraction using
``-TransitionGroupPicker:background_subtraction original`` as described in the
software comparison paper [7]_.

Ion Mobility parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~

If you are analyzing diaPASEF data, you can adjust the ion mobility extraction window using ``-ion_mobility_window``. In addition to using the iRT peptides for correction of the retention time space and the m/z space, OpenSWATH can also use those peptides to correct the ion mobility space with the option ``-Calibration:im_correction_function linear``. To include ion mobility feature scores, set the ``-Scoring:Scores:use_ion_mobility_scores`` flag.

MS1 and IPF parameters
~~~~~~~~~~~~~~~~~~~~~~

Furthermore, if you wish to use MS1 information, use the ``-use_ms1_traces`` flag, assuming that your input data contains an MS1 map in addition to the SWATH data. This is generally recommended. If you would like to enable IPF transition-level scoring and your spectral library was generated according to the IPF instructions, you should set the ``-enable_uis_scoring`` flag.

Example
~~~~~~~

Therefore, a full run of OpenSWATH may look like this:

.. tabs::

    .. code-tab:: bash DIA

        OpenSwathWorkflow.exe
        -in data.mzML -tr library.tsv
        -tr_irt iRT_assays.TraML
        -swath_windows_file SWATHwindows_analysis.tsv
        -out_features osw_output.osw
        -sort_swath_maps -batchSize 1000
        -readOptions cacheWorkingInMemory -tempDirectory C:\Temp
        -rt_extraction_window 600
        -mz_extraction_window 50 
        -mz_extraction_window_unit ppm
        -mz_correction_function quadratic_regression_delta_ppm
        -TransitionGroupPicker:background_subtraction original
        -RTNormalization:alignmentMethod linear
        -Scoring:stop_report_after_feature 5

    .. code-tab:: bash diaPASEF

        OpenSwathWorkflow.exe
        -in data.mzML -tr library.tsv
        -tr_irt iRT_assays.TraML
        -out_features osw_output.osw
        -sort_swath_maps -batchSize 1000
        -readOptions cacheWorkingInMemory -tempDirectory C:\Temp
        -pasef -force
        -rt_extraction_window 250
        -mz_extraction_window 25 
        -mz_extraction_window_unit ppm
        -mz_extraction_window_ms1 25 
        -mz_extraction_window_ms1_unit ppm 
        -mz_correction_function quadratic_regression_delta_ppm
        -ion_mobility_window 0.06 
        -im_extraction_window_ms1 0.06 
        -irt_mz_extraction_window 40 
        -irt_mz_extraction_window_unit ppm 
        -irt_im_extraction_window 0.2
        -Calibration:im_correction_function linear
        -Scoring:Scores:use_ion_mobility_scores true
        -TransitionGroupPicker:background_subtraction original
        -RTNormalization:estimateBestPeptides
        -RTNormalization:alignmentMethod lowess
        -RTNormalization:lowess:span 0.01 
        -Scoring:stop_report_after_feature 5
        -Scoring:Scores:use_ion_mobility_scores 


Troubleshooting
~~~~~~~~~~~~~~~

If you encounter issues with peak picking, try to disable peak filtering by
setting ``-Scoring:TransitionGroupPicker:compute_peak_quality false`` which will
disable the filtering of peaks by chromatographic quality. Furthermore, you
can adjust the smoothing parameters for the peak picking, by adjusting
``-Scoring:TransitionGroupPicker:PeakPickerMRM:sgolay_frame_length`` or using a
Gaussian smoothing based on your estimated peak width. Adjusting the signal
to noise threshold will make the peaks wider or smaller.

Output
------

The OpenSwathWorkflow produces two types of output: 

- identified peaks
- extracted chromatograms


the identified peaks can be stored in in SQLite format (*osw*) using or in a
XML format ( *featureXML*, not recommended) using the ``out_features`` flag. The file type will be determined by the file extension (either ``.osw`` or ``.featureXML``). 

the extracted chromatograms can be stored in mzML format using ``out_chrom``
with an ``.mzML`` extension. By default the produced mzML file will be numpress
compressed, but can be converted to regular mzML using the OpenMS
``FileConverter``. Alternatively, output can be written in ``.sqMass`` format,
which is a SQLite-based format.

Tutorial Data
-------------

Availability
~~~~~~~~~~~~

To learn OpenSWATH, we suggest to use the *M. tuberculosis* dataset published
alongside the 2017 Methods Mol Biol. OpenSWATH tutorial [4]_ which is available
from the PeptideAtlas raw data repository with accession number 
`PASS00779 <http://www.peptideatlas.org/PASS/PASS00779>`_.

The SWATH-MS Gold Standard and *Streptococcus pyogenes* data sets (used in the
original 2014 Nature Biotechnoly publication) are available from the
PeptideAtlas raw data repository with accession number 
`PASS00289 <http://www.peptideatlas.org/PASS/PASS00289>`_.

The Skyline results are available from `Skyline Panorama Webserver
<https://daily.panoramaweb.org/labkey/project/Aebersold/rosenberger/OpenSWATH_SGS/begin.view>`_.

The HeLa Evosep diaPASEF data is available from the `*Meier et al., Nature Methods (2020)* <https://www.nature.com/articles/s41592-020-00998-0#Sec10>`_ publication is available from the PRIDE repository with accession number `PXD017703 <https://www.ebi.ac.uk/pride/archive/projects/PXD017703>`_.

Mycobacterium tuberculosis data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- 3 mzML instrument data files (centroided)
- 3 WIFF raw instrument data files 
- Mtb assay library (for OpenMS 2.1)
- Mtb assay library (for older OpenMS)
- Swath windows file for analysis
- iRT assay file (TraML format)

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

HeLa Evosep diaPASEF data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- HeLa_Evosep_diaPASEF_RAW.zip 
  
  - 60SPD_py8: 3 tdf raw files (200ng HeLa)
  - 100SPD_py8: 3 tdf raw files (100ng HeLa)
  - 200SPD_py8: 3 tdf raw files (200ng HeLa)
  
- HeLa_Evosep_pqp_library.zip 
  
  - 200623_hela_evosep_fractions_library_ptypic_decoy.pqp
  
- HeLa_Evosep_pyprophet_export.zip
  
  - pyprophet_export_60SPD.tsv
  - pyprophet_export_100SPD.tsv
  - pyprophet_export_200SPD.tsv

References
----------
.. [1] Röst HL, Rosenberger G, Navarro P, Gillet L, Miladinović SM, Schubert OT, Wolski W, Collins BC, Malmström J, Malmström L, Aebersold R. OpenSWATH enables automated, targeted analysis of data-independent acquisition MS data. Nat Biotechnol. 2014 Mar 10;32(3):219-23. doi: 10.1038/nbt.2841. PMID: 24727770

.. [2] Gillet LC, Navarro P, Tate S, Röst H, Selevsek N, Reiter L, Bonner R, Aebersold R. Targeted data extraction of the MS/MS spectra generated by data-independent acquisition: a new concept for consistent and accurate proteome analysis. Mol Cell Proteomics. 2012 Jun;11(6):O111.016717. Epub 2012 Jan 18. PMID: 22261725

.. [3] Florian Meier, Andreas-David Brunner, Max Frank, Annie Ha, Eugenia Voytik, Stephanie Kaspar-Schoenefeld, Markus Lubeck, Oliver Raether, Ruedi Aebersold, Ben C. Collins, Hannes L. Röst, Matthias Mann. diaPASEF: parallel accumulation–serial fragmentation combined with data-independent acquisition. Nature Methods volume 17, pages 1229–1236 (2020). doi: https://www.nature.com/articles/s41592-020-00998-0

.. [4] Röst HL, Sachsenberg T, Aiche S, Bielow C, Weisser H, Aicheler F, Andreotti S, Ehrlich HC, Gutenbrunner P, Kenar E, Liang X, Nahnsen S, Nilse L, Pfeuffer J, Rosenberger G, Rurik M, Schmitt U, Veit J, Walzer M, Wojnar D, Wolski WE, Schilling O, Choudhary JS, Malmström L, Aebersold R, Reinert K, Kohlbacher O. OpenMS: a flexible open-source software platform for mass spectrometry data analysis. Nat Methods. 2016 Aug 30;13(9):741-8. doi: 10.1038/nmeth.3959. PMID: 27575624

.. [5] Röst HL, Aebersold R, Schubert OT. Automated SWATH Data Analysis Using Targeted Extraction of Ion Chromatograms. Methods Mol Biol. 2017;1550:289-307. doi: 10.1007/978-1-4939-6747-6_20. PMID: 28188537. `bioRxiv <http://biorxiv.org/content/early/2016/03/19/044552>`_. 

.. [6] Parker SJ, Rost H, Rosenberger G, Collins BC, Malmström L, Amodei D, Venkatraman V, Raedschelders K, Van Eyk JE, Aebersold R. Identification of a Set of Conserved Eukaryotic Internal Retention Time Standards for Data-independent Acquisition Mass Spectrometry. Mol Cell Proteomics. 2015 Oct;14(10):2800-13. doi: 10.1074/mcp.O114.042267. Epub 2015 Jul 21. PMID: 26199342

.. [7] Navarro P, Kuharev J, Gillet LC, Bernhardt OM, MacLean B, Röst HL, Tate SA, Tsou CC, Reiter L, Distler U, Rosenberger G, Perez-Riverol Y, Nesvizhskii AI, Aebersold R, Tenzer S.  A multicenter study benchmarks software tools for label-free proteome quantification.  Nat Biotechnol. 2016 Nov;34(11):1130-1136. doi: 10.1038/nbt.3685. Epub 2016 Oct 3.
