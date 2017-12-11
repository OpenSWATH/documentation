Skyline
=========

Overview
--------

`Skyline
<http://www.skyline.ms>`_ [1]_ is the most popular analysis software for targeted proteomics. It can be used to export spectral libraries suitable for OpenSWATH.

Contact and Support
-------------------

Skyline is supported by a very active community via their `message board
<https://skyline.ms/wiki/home/support/page.view?name=default>`_.

For ``TargetedFileConverter``, we provide support via the `OpenMS
<http://www.openms.de/support/>`_ support channels.

Tutorial
--------

The Skyline team provides extensive documentation and `tutorials
<https://skyline.ms/project/home/software/Skyline/begin.view>`_ and their website. Please follow their instructions to generate spectral or chromatogram libraries. To export a library for use in OpenSWATH, select ``File`` |larr| ``Report``. You can then import the report definition file ```OpenSWATH.skyr``
<https://github.com/OpenMS/OpenMS/blob/develop/tools/OpenSWATH.skyr>`_, which will directly export all necessary columns.

Convert then the Skyline transition list to a TraML spectral library and follow the remaining steps in the :doc:`generic` section:

.. code-block:: bash

	# TargetedFileConverter from OpenMS/develop
   TargetedFileConverter -in db_pqp.mrm -out db_pqp.TraML

References
----------
.. [1] MacLean B, Tomazela DM, Shulman N, Chambers M, Finney GL, Frewen B, Kern R, Tabb DL, Liebler DC, MacCoss MJ. Skyline: an open source document editor for creating and analyzing targeted proteomics experiments. Bioinformatics. 2010 Apr 1;26(7):966-8. doi: 10.1093/bioinformatics/btq054. Epub 2010 Feb 9. PMID: 20147306 