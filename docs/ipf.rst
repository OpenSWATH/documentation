IPF
=========

Overview
--------

IPF (Inference of PeptidoForms) [1]_ is an extension to the OpenSWATH [2]_ workflow to increase the specificity of the analysis to the level of peptidoforms (modified peptides with specific site-localization) across multiple runs. IPF is fully implemented as part of OpenMS [3]_ and PyProphet [4]_ and compatible with the downstream alignment algorithm TRIC [5]_.

Contact and Support
-------------------

We provide support for IPF using the `OpenMS support channels
<http://open-ms.sourceforge.net/support/bugs/>`_. Please address general questions to the open-ms-general mailing list.

You can contact the author `George Rosenberger
<http://www.rosenberger.pro>`_.

Installation
------------

IPF requires the installation of several tools:

OpenMS
~~~~~~~~~~

IPF is available since OpenMS 2.1. Please follow the instructions in the :doc:`openswath` tutorial to install OpenMS.

Percolator
~~~~~~~~~~

IPF presently requires QVALITY [6]_ to be installed, which is part of `Percolator
<http://www.percolator.ms>`_. Please install and export the binaries according to the Percolator instructions.

PyProphet
~~~~~~~~~~

IPF requires a specific branch of PyProphet. If Python and PIP are configured correctly, the following command can be used to install the latest version:

.. code-block:: bash

   pip install git+https://github.com/grosenberger/pyprophet.git@feature/ipf

To work properly, PyProphet needs to find the ``qvality`` executable in the main path.

TRIC
~~~~

TRIC should be installed according to the :doc:`tric` installation instructions.


Tutorial
--------

Running IPF requires following several steps:

1. Peptide Query Parameter Generation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

IPF requries a spectral library generated from DDA (or DIA pseudo spectra, e.g. from DIA-Umpire [7]_) data. The protocol is described in more detail in the :doc:`tpp` tutorial.

Using SpectraST, the spectral library ``db_consensus.splib`` is converted to a MRM and then to a TraML file:

.. code-block:: bash

   # This will generate the file db_assays.mrm
   spectrast -cNdb_assays -cICID-QTOF -cM db_consensus.splib

   ConvertTSVToTraML -in db_assays.mrm -out db_assays.TraML

Then, a TraML file containing the detection and identification transitions is being generated. At this step, the residue modifiability needs to be defined in the `OpenMS resource directory
<https://github.com/OpenMS/OpenMS/tree/develop/share/OpenMS>`_. For this purpose, the files ``CHEMISTRY/PSI-MOD.obo`` and ``CHEMISTRY/unimod.xml`` can be manually modified. An example for phosphorylation can be obtained from the ProteomeXchange repository. The location of the modified ``OpenMS resource directory`` needs to be supplied by setting ``OPENMS_DATA_PATH`` for ``OpenSwathAssayGenerator``:

.. code-block:: bash

   OPENMS_DATA_PATH=~/modified_path/share \
   OpenSwathAssayGenerator -in db_assays.TraML \
   -out db_assays_ptms.TraML \
   -swath_windows_file swath64.txt \
   -allowed_fragment_charges 1,2,3,4 \
   -enable_ms1_uis_scoring \
   -max_num_alternative_localizations 2000 \
   -enable_identification_specific_losses \
   -enable_identification_ms2_precursors

We then append decoys to the library:

.. code-block:: bash

   OPENMS_DATA_PATH=~/modified_path/share \
   OpenSwathDecoyGenerator -in db_assays_ptms.TraML \
   -out db_assays_ptms_decoys.TraML \
   -method shuffle \
   -append \
   -mz_threshold 0.1 \
   -remove_unannotated

2. Targeted data extraction using OpenSWATH
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The next step is conducted using OpenSWATH. 

.. code-block:: bash

   OPENMS_DATA_PATH=~/modified_path/share \
   OpenSwathWorkflow -min_upper_edge_dist 1 \
   -mz_extraction_window 0.05 \
   -rt_extraction_window 600 \
   -extra_rt_extraction_window 100 \
   -min_rsq 0.95 \
   -min_coverage 0.6 \
   -use_ms1_traces \
   -enable_uis_scoring \
   -Scoring:uis_threshold_peak_area 0 \
   -Scoring:uis_threshold_sn 0 \
   -Scoring:stop_report_after_feature 5 \
   -tr_irt DIA_iRT.TraML \
   -tr db_assays_ptms_decoys.TraML \
   -threads 8 \
   -in MSDATA.mzXML.gz \
   -out_tsv MSDATA_RESULTS.tsv

Important is to set the parameters ``-use_ms1_traces`` and ``-enable_uis_scoring`` to extract the additional identification transitions and precursor signals using OpenSWATH.

3. Statistical validation using PyProphet
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
PyProphet is then applied to the OpenSWATH results:

.. code-block:: bash

   pyprophet --target.overwrite \
   --final_statistics.emp_p \
   --qvality.enable \
   --qvality.generalized \
   --ms1_scoring.enable \
   --uis_scoring.enable \
   --d_score.cutoff=100000 \
   --semi_supervised_learner.num_iter=20 \
   --xeval.num_iter=20 \
   --ignore.invalid_score_columns \
   --uis_scoring.expand_peptidoforms MSDATA_RESULTS.tsv

It generates reports on several different levels. Important for TRIC are the files that end with ``*_uis_expanded.csv``. IPF attaches several columns, e.g. ``PosteriorFullPeptideName``, which contains the peptidoform sequence of the best scoring peptidoform. The column ``pfqm_score`` represents the peptidoform q-value, whereas ``pf_score`` represent the posterior probability. After running IPF, the ``m_score`` column is equal to ``pfqm_score`` to enable alignment by TRIC.

4. Multi-run alignment using TRIC
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

TRIC can be applied to the IPF results with the following command:

.. code-block:: bash

   feature_alignment.py --in *_uis_expanded.csv \
   --out feature_alignment.csv \
   --out_matrix feature_alignment_matrix.csv \
   --file_format openswath \
   --fdr_cutoff 0.01 \
   --max_fdr_quality 0.2 \
   --mst:useRTCorrection True \
   --mst:Stdev_multiplier 3.0 \
   --method LocalMST \
   --max_rt_diff 30 \
   --alignment_score 0.0001 \
   --frac_selected 0 \
   --realign_method lowess_cython \
   --disable_isotopic_grouping

Data
----
Availability
~~~~~~~~~~~~

The synthetic phosphopeptide reference mass spectrometry proteomics data is available from PRIDE/ProteomeXchange with the data set identifier `PXD004573
<https://www.ebi.ac.uk/pride/archive/projects/PXD004573>`_.

The enriched U2OS phosphopeptide mass spectrometry proteomics data is available from PRIDE/ProteomeXchange with the data set identifier `PXD006056
<https://www.ebi.ac.uk/pride/archive/projects/PXD006056>`_.

The 14-3-3β phosphopeptide interactomics mass spectrometry proteomics data is available from PRIDE/ProteomeXchange with the data set identifier `PXD006057
<https://www.ebi.ac.uk/pride/archive/projects/PXD006057>`_.

The twin study mass spectrometry proteomics data is available from PRIDE/ProteomeXchange with the data set identifier `PXD004574
<https://www.ebi.ac.uk/pride/archive/projects/PXD004574>`_.

References
----------

.. [1] Inference and quantification of peptidoforms in large sample cohorts by SWATH-MS. Rosenberger G, Liu Y, Röst HL, Ludwig C, Buil A, Bensimon A, Soste M, Spector TD, Dermitzakis ET, Collins BC, Malmström L, Aebersold R. Nat Biotechnol. 2017 Jun 12. doi: 10.1038/nbt.3908. [Epub ahead of print] PMID: 28604659

.. [2] Röst HL, Rosenberger G, Navarro P, Gillet L, Miladinović SM, Schubert OT, Wolski W, Collins BC, Malmström J, Malmström L, Aebersold R. OpenSWATH enables automated, targeted analysis of data-independent acquisition MS data. Nat Biotechnol. 2014 Mar 10;32(3):219-23. doi: 10.1038/nbt.2841. PMID: 24727770

.. [3] Röst HL, Sachsenberg T, Aiche S, Bielow C, Weisser H, Aicheler F, Andreotti S, Ehrlich HC, Gutenbrunner P, Kenar E, Liang X, Nahnsen S, Nilse L, Pfeuffer J, Rosenberger G, Rurik M, Schmitt U, Veit J, Walzer M, Wojnar D, Wolski WE, Schilling O, Choudhary JS, Malmström L, Aebersold R, Reinert K, Kohlbacher O. OpenMS: a flexible open-source software platform for mass spectrometry data analysis. Nat Methods. 2016 Aug 30;13(9):741-8. doi: 10.1038/nmeth.3959. PMID: 27575624

.. [4] Teleman J, Röst HL, Rosenberger G, Schmitt U, Malmström L, Malmström J, Levander F. DIANA--algorithmic improvements for analysis of data-independent acquisition MS data. Bioinformatics. 2015 Feb 15;31(4):555-62. doi: 10.1093/bioinformatics/btu686. Epub 2014 Oct 27. PMID: 25348213

.. [5] Röst HL, Liu Y, D'Agostino G, Zanella M, Navarro P, Rosenberger G, Collins BC, Gillet L, Testa G, Malmström L, Aebersold R. TRIC: an automated alignment strategy for reproducible protein quantification in targeted proteomics. Nat Methods. 2016 Sep;13(9):777-83. doi: 10.1038/nmeth.3954. Epub 2016 Aug 1. PMID: 27479329

.. [6] Käll L, Storey JD, Noble WS. QVALITY: non-parametric estimation of q-values and posterior error probabilities. Bioinformatics. 2009 Apr 1;25(7):964-6. doi: 10.1093/bioinformatics/btp021. Epub 2009 Feb 4. PMID: 19193729

.. [7] Tsou CC, Avtonomov D, Larsen B, Tucholska M, Choi H, Gingras AC, Nesvizhskii AI. DIA-Umpire: comprehensive computational framework for data-independent acquisition proteomics. Nat Methods. 2015 Mar;12(3):258-64, 7 p following 264. doi: 10.1038/nmeth.3255. Epub 2015 Jan 19. PMID: 25599550
