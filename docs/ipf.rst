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

IPF is fully integrated within the tools of the OpenSWATH workflow. Please follow the :doc:`openswath`, :doc:`pyprophet` and :doc:`tric` installation instructions for the latest development branches. The current instructions are written for the new SQLite-based workflow. You can alternatively follow the instructions for the :doc:`ipf_legacy`.

Tutorial
--------

Running IPF requires to modify the parameters of several steps of tools part of the OpenSWATH workflow:

1. Peptide Query Parameter Generation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

IPF requries a spectral library generated from DDA (or DIA pseudo spectra, e.g. from DIA-Umpire [7]_) data. The input can come for example from :doc:`tpp`, :doc:`skyline` or can be provided in the form of :doc:`generic`. The underlying PSMs do not need to be site-localized, as IPF will assess site-localization independently. However, a site-localized spectral library might provide better peptide query parameters.

The first step uses ``OpenSwathAssayGenerator`` to append *in silico* ``identification transitions`` to the spectral library. The required parameters (including residue modifiability) and considerations are described in the section :doc:`pqp`. The spectral library should also be appended with decoys and converted to a ``PQP`` file.


2. Targeted data extraction using OpenSWATH
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The next step is conducted using OpenSWATH. Follow the IPF-specific instructions in the section :doc:`openswath_workflow`. Important is to enable MS1 and transition-level scoring by setting the parameters ``-use_ms1_traces`` and ``-enable_uis_scoring`` for ``OpenSwathWorkflow``. Make sure to use the ``PQP`` spectral library as input and write an ``OSW`` file as output.

3. Statistical validation using PyProphet
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
PyProphet is then applied to the OpenSWATH results. Follow the IPF-specific instructions in the :doc:`pyprophet` section. Export a legacy TSV report for analysis with TRIC.

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

Note that IPF does not report decoys, which is the reason why ``max_fdr_quality`` must be set.

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

.. [1] Rosenberger G, Liu Y, Röst HL, Ludwig C, Buil A, Bensimon A, Soste M, Spector TD, Dermitzakis ET, Collins BC, Malmström L, Aebersold R. Inference and quantification of peptidoforms in large sample cohorts by SWATH-MS. Nat Biotechnol. 2017 Aug;35(8):781-788. doi: 10.1038/nbt.3908. Epub 2017 Jun 12. PMID: 28604659

.. [2] Röst HL, Rosenberger G, Navarro P, Gillet L, Miladinović SM, Schubert OT, Wolski W, Collins BC, Malmström J, Malmström L, Aebersold R. OpenSWATH enables automated, targeted analysis of data-independent acquisition MS data. Nat Biotechnol. 2014 Mar 10;32(3):219-23. doi: 10.1038/nbt.2841. PMID: 24727770

.. [3] Röst HL, Sachsenberg T, Aiche S, Bielow C, Weisser H, Aicheler F, Andreotti S, Ehrlich HC, Gutenbrunner P, Kenar E, Liang X, Nahnsen S, Nilse L, Pfeuffer J, Rosenberger G, Rurik M, Schmitt U, Veit J, Walzer M, Wojnar D, Wolski WE, Schilling O, Choudhary JS, Malmström L, Aebersold R, Reinert K, Kohlbacher O. OpenMS: a flexible open-source software platform for mass spectrometry data analysis. Nat Methods. 2016 Aug 30;13(9):741-8. doi: 10.1038/nmeth.3959. PMID: 27575624

.. [4] Teleman J, Röst HL, Rosenberger G, Schmitt U, Malmström L, Malmström J, Levander F. DIANA--algorithmic improvements for analysis of data-independent acquisition MS data. Bioinformatics. 2015 Feb 15;31(4):555-62. doi: 10.1093/bioinformatics/btu686. Epub 2014 Oct 27. PMID: 25348213

.. [5] Röst HL, Liu Y, D'Agostino G, Zanella M, Navarro P, Rosenberger G, Collins BC, Gillet L, Testa G, Malmström L, Aebersold R. TRIC: an automated alignment strategy for reproducible protein quantification in targeted proteomics. Nat Methods. 2016 Sep;13(9):777-83. doi: 10.1038/nmeth.3954. Epub 2016 Aug 1. PMID: 27479329

.. [6] Käll L, Storey JD, Noble WS. QVALITY: non-parametric estimation of q-values and posterior error probabilities. Bioinformatics. 2009 Apr 1;25(7):964-6. doi: 10.1093/bioinformatics/btp021. Epub 2009 Feb 4. PMID: 19193729

.. [7] Tsou CC, Avtonomov D, Larsen B, Tucholska M, Choi H, Gingras AC, Nesvizhskii AI. DIA-Umpire: comprehensive computational framework for data-independent acquisition proteomics. Nat Methods. 2015 Mar;12(3):258-64, 7 p following 264. doi: 10.1038/nmeth.3255. Epub 2015 Jan 19. PMID: 25599550
