Percolator
=========

Overview
--------

Percolator [1]_, [2]_ is a popular algorithm for statistical validation of shotgun proteomic data. Using the OpenMS TOPP tool ``PercolatorAdapter``, Percolator can score the results from OpenSWATH and thus be used instead of PyProphet.

Contact and Support
-------------------

OpenSWATH support in ``PercolatorAdapter`` is currently in development and must NOT be used in production environments. We would however be very grateful for testing of the tool and reporting of problems and bugs.

We provide support for ``PercolatorAdapter`` using the `OpenMS support channels
<http://www.openms.de/support/>`_. Please address general questions to the open-ms-general mailing list.

Please address any general Percolator inquiries to the `Percolator team
<http://percolator.ms/>`_.

Installation
------------
PercolatorAdapter
~~~~~~~~~~~~~~~~~
``PercolatorAdapter`` is available in the OpenMS development branch. Please refer to the :doc:`openswath` tutorial for installation instructions.

PyProphet
~~~~~~~~~
To convert the results of ``PercolatorAdapter`` to an OpenSWATH TSV report, the SQLite-enabled ``PyProphet`` version is required. Please refer to the :doc:`sqlite` tutorial for installation instructions.

Tutorial
--------
PercolatorAdapter
~~~~~~~~~~~~~~~~~
After installation, ``PercolatorAdapter`` can be run on the OSW results using the following commands:

.. code-block:: bash

   # Score on MS2-level
   PercolatorAdapter --in_osw openswath_results.osw --out openswath_results.osw \
   --osw_level ms2

   # Score on MS1-level
   PercolatorAdapter --in_osw openswath_results.osw --out openswath_results.osw \
   --osw_level ms1

   # Score on transition-level
   PercolatorAdapter --in_osw openswath_results.osw --out openswath_results.osw \
   --osw_level transition

PyProphet
~~~~~~~~~
If IPF should be applied after scoring, the following command can be used:

.. code-block:: bash

   pyprophet ipf --in=merged.osw


Finally, we can export the results to a legacy OpenSWATH TSV report:

.. code-block:: bash

   pyprophet export --in=merged.osw --out=legacy.tsv \


References
----------
.. [1] Käll L, Canterbury JD, Weston J, Noble WS, MacCoss MJ. Semi-supervised learning for peptide identification from shotgun proteomics datasets. Nat Methods. 2007 Nov;4(11):923-5. Epub 2007 Oct 21. PMID: 17952086

.. [2] The M, MacCoss MJ, Noble WS, Käll L. Fast and Accurate Protein False Discovery Rates on Large-Scale Proteomics Data Sets with Percolator 3.0. J Am Soc Mass Spectrom. 2016 Nov;27(11):1719-1727. Epub 2016 Aug 29. PMID: 27572102
