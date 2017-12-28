Integrated OpenSWATH Workflow
================

Overview
--------

The OpenSwathWorkflow executable is currently the most efficient way of running
OpenSWATH [1]_ [2]_ and it is available through OpenMS [3]_.  An extended
tutorial describing a complete OpenSWATH analysis workflow using
``OpenSwathWorkflow`` was recently published [4]_ and is also available from
`bioRxiv <http://biorxiv.org/content/early/2016/03/19/044552>`_ with its
associated `dataset <http://www.peptideatlas.org/PASS/PASS00779>`_. 

The OpenSwathWorkflow implements the OpenSWATH analysis workflow as described
in [1]_ and provides a complete, integrated analysis tool without the need to 
run multiple tools consecutively.

It executes the following steps in order:

 - Reading of the raw input file (provided as mzML, mzXML or sqMass) and RT normalization transition list
 - Computing the retention time transformation using RT normalization peptides
 - Reading of the transition list
 - Extracting the specified transitions
 - Scoring the peak groups in the extracted ion chromatograms (XIC)
 - Reporting the peak groups and the chromatograms

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

Spectral library
~~~~~~~~~~~~~~~~

The spectral library ``tr`` is a spectral library either in ``.tsv``,
``.TraML`` or ``.PQP`` format (where the ``TSV`` or ``PQP`` format is recommended). Further information in generating these files can be found in the :doc:`generic` section.

Retention time normalization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The retention time normalization peptides are provided using the optional
parameter ``tr_irt`` in TraML format. We suggest to use the ``iRTassays.TraML`` file provided in
the tutorial dataset, if the Biognosys iRT-kit was used during sample preparation. If the iRT-kit was not used, it is highly recommended to use or generate a set of endogenous peptides for RT normalization. A recent publication [5]_ provides such a set of ``CiRT`` peptides suitable for many eukaryotic samples.

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
software comparison paper [6]_.

MS1 and IPF parameters
~~~~~~~~~~~~~~~~~~~~~~

Furthermore, if you wish to use MS1 information, use the ``-use_ms1_traces`` flag, assuming that your input data contains an MS1 map in addition to the SWATH data. This is generally recommended. If you would like to enable IPF transition-level scoring and your spectral library was generated according to the IPF instructions, you should set the ``-enable_uis_scoring`` flag.

Example
~~~~~~~

Therefore, a full run of OpenSWATH may look like this:

.. code-block:: bash

    OpenSwathWorkflow.exe
    -in data.mzML -tr library.tsv
    -tr_irt iRT_assays.TraML
    -swath_windows_file SWATHwindows_analysis.tsv
    -sort_swath_maps -batchSize 1000
    -readOptions cacheWorkingInMemory -tempDirectory C:\Temp
    -use_ms1_traces
    -mz_extraction_window 50 -ppm
    -mz_correction_function quadratic_regression_delta_ppm
    -TransitionGroupPicker:background_subtraction original
    -RTNormalization:alignmentMethod linear
    -out_tsv osw_output.tsv

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


the identified peaks can be stored in tsv format using ``-out_tsv``
(recommended), in SQLite format using ``--out_osw`` (experimental) or in a
featureXML format using ``-out_features`` (not recommended).

the extracted chromatograms can be stored in mzML format using ``out_chrom``
with an ``.mzML`` extension. By default the produced mzML file will be numpress
compressed, but can be converted to regular mzML using the OpenMS
``FileConverter``. Alternatively, output can be written in ``.sqMass`` format,
which is a SQLite-based format (experimental).

References
----------
.. [1] Röst HL, Rosenberger G, Navarro P, Gillet L, Miladinović SM, Schubert OT, Wolski W, Collins BC, Malmström J, Malmström L, Aebersold R. OpenSWATH enables automated, targeted analysis of data-independent acquisition MS data. Nat Biotechnol. 2014 Mar 10;32(3):219-23. doi: 10.1038/nbt.2841. PMID: 24727770

.. [2] Gillet LC, Navarro P, Tate S, Röst H, Selevsek N, Reiter L, Bonner R, Aebersold R. Targeted data extraction of the MS/MS spectra generated by data-independent acquisition: a new concept for consistent and accurate proteome analysis. Mol Cell Proteomics. 2012 Jun;11(6):O111.016717. Epub 2012 Jan 18. PMID: 22261725

.. [3] Röst HL, Sachsenberg T, Aiche S, Bielow C, Weisser H, Aicheler F, Andreotti S, Ehrlich HC, Gutenbrunner P, Kenar E, Liang X, Nahnsen S, Nilse L, Pfeuffer J, Rosenberger G, Rurik M, Schmitt U, Veit J, Walzer M, Wojnar D, Wolski WE, Schilling O, Choudhary JS, Malmström L, Aebersold R, Reinert K, Kohlbacher O. OpenMS: a flexible open-source software platform for mass spectrometry data analysis. Nat Methods. 2016 Aug 30;13(9):741-8. doi: 10.1038/nmeth.3959. PMID: 27575624

.. [4] Röst HL, Aebersold R, Schubert OT. Automated SWATH Data Analysis Using Targeted Extraction of Ion Chromatograms. Methods Mol Biol. 2017;1550:289-307. doi: 10.1007/978-1-4939-6747-6_20. PMID: 28188537. `bioRxiv <http://biorxiv.org/content/early/2016/03/19/044552>`_. 

.. [5] Parker SJ, Rost H, Rosenberger G, Collins BC, Malmström L, Amodei D, Venkatraman V, Raedschelders K, Van Eyk JE, Aebersold R. Identification of a Set of Conserved Eukaryotic Internal Retention Time Standards for Data-independent Acquisition Mass Spectrometry. Mol Cell Proteomics. 2015 Oct;14(10):2800-13. doi: 10.1074/mcp.O114.042267. Epub 2015 Jul 21. PMID: 26199342

.. [6] Navarro P, Kuharev J, Gillet LC, Bernhardt OM, MacLean B, Röst HL, Tate SA, Tsou CC, Reiter L, Distler U, Rosenberger G, Perez-Riverol Y, Nesvizhskii AI, Aebersold R, Tenzer S.  A multicenter study benchmarks software tools for label-free proteome quantification.  Nat Biotechnol. 2016 Nov;34(11):1130-1136. doi: 10.1038/nbt.3685. Epub 2016 Oct 3.

