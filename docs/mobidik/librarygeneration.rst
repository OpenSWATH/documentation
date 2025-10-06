Legacy Library Generation (diaPASEF)
==================

For most data-independent acquisition (DIA) analysis, a well-represented 
spectral library is required for precursors, peptide, and protein
identifications. Currently, we support library generation with the ``diapysef``
package directly from a MaxQuant analysis of several DDA-PASEF runs. Make sure
``diapysef`` is installed before you proceed.

The general steps for generating the spectral library are, (i) annotating ion
mobility values in the MQ output, (ii) correcting retention time and ion mobility 
values against iRT peptides, (iii) formatting it to OpenSwath read-able format, (iv)
generate the spectral library formats (.TraML, .pqp, .tsv) with OpenSwath.


Annotating Ion Mobility
^^^^^^^^^^^^^^^^^^^^^^^
Generating the library requires the DDA output files from MaxQuant as well as
one of the original DDA PASEF files.

- msms.txt
- allPeptides.txt
- evidence.txt
- .d folder (DDA PASEF)

The library generation script can then be called:

.. code:: bash

   python create_library.py --pasefdata data.d --mqout path/to/maxquant_data --irt irt_file.tsv 

For details and options of the script, simply type:

.. code:: bash

   python create_library.py --help


Generating Assay Library
^^^^^^^^^^^^^^^^^^^^^^^^

After generating the spectral library with diapysef, the ``tsv`` file can be imported into ``OpenSwathAssayGenerator`` and ``OpenSwathDecoyGenerator`` as documented in :doc:`pqp`

