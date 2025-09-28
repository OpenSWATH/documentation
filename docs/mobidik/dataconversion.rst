MOBI-DIK Data Conversion
========================

For the Mobi-DIK workflow, the generated .tdf files from diaPASEF runs can be
converted to standard formats (mzML) with ``diapysef``. Make sure ``diapysef``
is installed before you proceed.

Using the ``diapysef converttdftomzml`` command can convert the .tdf file to
a single mzML file. It allows merging of frames for the same precursors, 
filtering of range of frames, splitting of files by overlapping window 
settings, and compression of data with PyMSNumpress.

Inputs
------
- ``--in``: The location of the directory containing raw data (usually .d).  [required]
- ``--out``: The name of the output file (mzML). [required]
- ``--merge``: Number of consecutive frames to sum up (squash). This is useful to boost S/N if exactly repeated frames are measured. [default: -1]
- ``--keep_frames / --no-keep_frames``: Whether to store frames exactly as measured or split them into individual spectra by precursor isolation window (default is to split them - this is almost always what you want).  [default: no-keep_frames]
- ``-overlap``: How many overlapping windows were recorded for the same m/z window - will split the output into N output files.  [default: -1]
- ``--framerange``: The minimum and maximum Frames to convert. Useful to only convert a part of a file. [default: [-1, -1]]

For detailed options and descriptions, simply type:

.. code:: bash

   diapysef converttdftomzml --help


Example
-------

.. code:: bash

   data_dir=diaPasef_run.d
   output_file='diaPasef_run.mzML'

   diapysef converttdftomzml --in=$data_dir --out=$output_file


The converted mzML files can be processed with the assay library in ``OpenSwathWorkflow``.

