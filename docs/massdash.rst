MassDash
=========

Overview
--------

MassDash [1]_, a cross-platform DIA mass spectrometry visualization and validation software for comparing features and results across popular tools. MassDash provides a web-based interface and Python package (for jupyter notebook integration) for interactive feature visualizations and summary report plots across multiple automated DIA feature detection tools, including OpenSwath, DIA-NN, and dreamDIA. Furthermore, MassDash processes peptides on the fly, enabling interactive visualization of peptides across dozens of runs simultaneously on a personal computer. MassDash supports various multidimensional visualizations across retention time, ion mobility, m/z, and intensity, providing additional insights into the data. The modular framework is easily extendable, enabling rapid algorithm development of novel peak-picker techniques, such as deep-learning-based approaches and refinement of existing tools.

.. image:: https://www.biorxiv.org/content/biorxiv/early/2024/01/17/2024.01.15.575772/F1.large.jpg?width=800&height=600&carousel=1
    :alt: MassDash: A Comprehensive Toolkit for Streamlined DIA-MS Visualization, Analysis, Optimization, and Rapid Prototyping.
    
    This schematic provides an overview of the MassDash toolkit and its versatile workflows. Accessible through a graphical user interface (GUI) deployed as a web application either locally or remotely, MassDash accepts mass spectrometry DIA/diaPASEF data, whether raw or in the form of post-extracted ion chromatograms. The tool offers diverse visualization options, including 1D, 2D, or 3D plots tailored to the data type. Raw data parameter optimization empowers users to finely tune and explore the dataset before initiating comprehensive targeted data extraction. Beyond its user-friendly interface, MassDash serves as a Python library, facilitating rapid algorithm development and testing. Users can delve into and compare search results derived from different methods, enhancing the tool’s utility for robust data exploration and analysis. Created with BioRender.com (2024).

Contact and Support
-------------------

We provide support for MassDash on the `GitHub repository
<https://github.com/Roestlab/massdash/issues>`_ through GitHub issues or you 
can contact the authors `Justin Sing <https://singjc.github.io/>`_ and `Joshua Charkow <https://ca.linkedin.com/in/joshua-charkow-7a050a219>`_.

Installation
------------

See the `GitHub README Installation instructions <https://github.com/Roestlab/massdash?tab=readme-ov-file#recommended-pip-installation>`_.

Tutorial
--------

A tutorial is available on the ReadTheDocs documentation page for the `GUI <https://massdash.readthedocs.io/en/latest/GUI.html>`_ and the `Python inteface <https://massdash.readthedocs.io/en/latest/Python%20Interface.html>`_.

References
----------
.. [1] Sing JC, Charkow J, AlHigaylan M, Horecka I, Xu L, Röst HL. MassDash: A Web-Based Dashboard for Data-Independent Acquisition Mass Spectrometry Visualization. J Proteome Res. 2024 Jun 7;23(6):2306-2314. doi: 10.1021/acs.jproteome.4c00026. Epub 2024 Apr 29.
