Binaries
========

Overview
--------

Precompiled or prepackaged versions of OpenMS, PyProphet and msproteomicstools are readily available for most supported platforms. 

.. warning::

   Please note that the published versions are always based on the latest `release` versions, however some functionality described online might only be available in the latest `development` builds.

OpenMS
------
OpenSWATH is completely integrated into `OpenMS <http://www.openms.org>`_. The current releases can be obtained from `GitHub <https://github.com/OpenMS/OpenMS/releases>`_. To make use of the latest developments, consider using the `nightly builds for Windows and macOS <https://abibuilder.informatik.uni-tuebingen.de/archive/openms/OpenMSInstaller/nightly/>`_.

Instructions for compilation and installation can be obtained from the `OpenMS documentation <http://ftp.mi.fu-berlin.de/pub/OpenMS/release-documentation/html/index.html>`_.

PyProphet
---------

PyProphet requires Python 2.7 or Python 3. Windows users should install Anaconda. Mac and Linux users should be able to install PyProphet directly from GitHub. It is strongly recommended to install PyProphet within a `virtualenv <https://virtualenv.pypa.io/en/stable/userguide/>`_.

.. code-block:: bash

   # Install dependencies
   pip install numpy scipy scikit-learn pandas numexpr statsmodels Click matplotlib seaborn

   # Install PyProphet development version
   pip install git+https://github.com/grosenberger/pyprophet.git@feature/refactoring


msproteomicstools & TRIC
------------------------
msproteomicstools requires Python 2.7 and can be installed through ``pip``. On Microsoft Windows you will first have to install Python (the easiest way to do this is to download `Anaconda <https://www.continuum.io/anaconda-overview>`_). You can then install msproteomicstools through `PyPI <https://pypi.python.org/pypi/msproteomicstools>`_:

.. code-block:: bash

   pip install numpy
   pip install msproteomicstools

You can alternatively download the msproteomicstools release directly from PyPI. To obtain the latest development version, please download the code from `GitHub <https://github.com/msproteomicstools/msproteomicstools>`_. If you are using Microsoft Windows and Anaconda, it is possible that BioPython does not properly install and you may have to install it through Anaconda:

.. code-block:: bash

   conda install biopython

