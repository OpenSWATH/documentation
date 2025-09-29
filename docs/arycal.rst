ARYCAL
=========

Overview
--------

ARYCAL (Across Run dYnamic Chromatogram ALignment) is a Rust-based tool for aligning extracted ion chromatograms (EICs) across multiple runs from targeted DIA mass spectrometry data. ARYCAL is based on similar principles as DIAlignR [1]_, using dynamic programming to align precursor chromatographic traces across multiple runs. In addition, ARYCAL supports the use of fast Fourier transform (FFT) for alignment, which can reduce the time required for alignment.

Contact and Support
-------------------

We provide support for DIAlignR on the `GitHub repository
<https://github.com/singjc/arycal/issues>`_.


You can contact the author `Justin Sing <https://singjc.github.io/>`_.

Tutorial
--------
You can find installation and usaged instructions on the `GitHub repository README <https://github.com/singjc/arycal?tab=readme-ov-file#usage>`_.

References
----------
.. [1] Gupta S, Ahadi S, Zhou W, Röst H. "DIAlignR Provides Precise Retention Time Alignment Across Distant Runs in DIA and Targeted Proteomics." Mol Cell Proteomics. 2019 Apr;18(4):806-817. doi: https://doi.org/10.1074/mcp.TIR118.001132 Epub 2019 Jan 3

.. [2] Gupta, S., Sing, J.C. & Röst, H.L. Achieving quantitative reproducibility in label-free multisite DIA experiments through multirun alignment. Commun Biol 6, 1101 (2023). https://doi.org/10.1038/s42003-023-05437-2
