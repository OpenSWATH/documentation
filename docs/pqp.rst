Peptide Query Parameter Generation
----------------------------------

PQP Generation for OpenSWATH
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After importing transitions lists from an upstream workflow (e.g. :doc:`tpp`, :doc:`skyline` or :doc:`generic`), the transitions can then be optimized using a set of heuristic rules [1]_:

.. code-block:: bash

   OpenSwathAssayGenerator -in transitionlist.TraML \
   -out transitionlist_optimized.TraML \
   -swath_windows_file swath64.txt \
   
Please note that the SWATH windows file should be of the following format (tab-separated), including header:

.. code-block:: bash

   lower_offset upper_offset
   400 425
   424 450
   ...
   ...

If necessary, the rules for transition selection can be modified with the following parameters:
   
.. code-block:: bash

   OpenSwathAssayGenerator <other parameters>
   -min_transitions 6 \
   -max_transitions 6 \
   -allowed_fragment_types b,y \
   -allowed_fragment_charges 1,2,3,4 \
   -enable_detection_specific_losses \
   -enable_detection_unspecific_losses \
   -precursor_mz_threshold 0.025 \
   -precursor_lower_mz_limit 400 \
   -precursor_upper_mz_limit 1200 \
   -product_mz_threshold 0.025 \
   -product_lower_mz_limit 350 \
   -product_upper_mz_limit 2000 \

PQP Generation for IPF
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If IPF scoring [2]_ should be conducted, the following parameters should be considered:

.. code-block:: bash

   OpenSwathAssayGenerator <other parameters>
   -enable_ipf \
   -unimod_file unimod_phospho.xml \

Unimod contains descriptions of more than 1400 post-translational modifications and represents the standard database. However, many modification types are annotated with residue modifiabilites that go beyond the canonical set (e.g. phosphorylation (S,T,Y,D,H,C,R,K) instead of (S,T,Y)).

For the purpose of site-localization, it is thus very important to provide a modified (restricted) Unimod file to ``OpenSwathAssayGenerator``. This file can be created by editing the file `unimod.xml<https://raw.githubusercontent.com/OpenMS/OpenMS/develop/share/OpenMS/CHEMISTRY/unimod.xml>`_ to only the desired modifications and residue modifiabilites. We provide an `example <https://github.com/OpenMS/OpenMS/blob/develop/src/tests/topp/OpenSwathAssayGenerator_input_3_unimod.xml>`_ for Phosphorylation (S,T,Y), Carbamidomethyl (C), Oxidation (M) and SILAC (R, K). Please note that also usually fixed PTMs like Carbamidomethyl and Oxidation need to be set.

The generation of ``identification transitions`` can be adjusted if necessary by the following parameters:

.. code-block:: bash

   OpenSwathAssayGenerator <other parameters>
   -enable_ipf \
   -unimod_file unimod_phospho.xml \
   -max_num_alternative_localizations 10000 \
   -disable_identification_ms2_precursors \
   -disable_identification_specific_losses \
   -enable_identification_unspecific_losses \
   -enable_swath_specifity \

``OpenSwathAssayGenerator`` excludes peptides that can have too many combinations of alternative site-localization (track the process by setting ``-debug 10``). If 10000 alternative peptidoforms are too few, consider increasing this parameter. 

By default, unfragmented precursors are extracted from the SWATH maps and used for scoring by IPF, this can optionally be disabled (``-disable_identification_ms2_precursors``). Specific losses (e.g. for Phosphorylation) are used by default and improve specificity; unspecific losses are not recommended to use.

In scenarios with extremely small precursor isolation windows (e.g. < 1 Th), ``-enable_swath_specificity`` can be used to skip the precursor inference step of IPF. This is not recommended in general.

Decoy Generation
~~~~~~~~~~~~~~~~

Decoys can then be appended using ``OpenSwathDecoyGenerator``:

.. code-block:: bash

   OpenSwathDecoyGenerator -in transitionlist_optimized.TraML \
   -out transitionlist_optimized_decoys.TraML \
   
You can then convert the TraML to a PQP file:

.. code-block:: bash

   TargetedFileConverter -in transitionlist_optimized_decoys.TraML \
   -out transitionlist_optimized_decoys.PQP \

This processed spectral library (including decoys) is the input for :doc:`openswath`.

References
----------
.. [1] Schubert OT, Gillet LC, Collins BC, Navarro P, Rosenberger G, Wolski WE, Lam H, Amodei D, Mallick P, MacLean B, Aebersold R. Building high-quality assay libraries for targeted analysis of SWATH MS data. Nat Protoc. 2015 Mar;10(3):426-41. doi: 10.1038/nprot.2015.015. Epub 2015 Feb 12. PMID: 25675208

.. [2] Rosenberger G, Liu Y, Röst HL, Ludwig C, Buil A, Bensimon A, Soste M, Spector TD, Dermitzakis ET, Collins BC, Malmström L, Aebersold R. Inference and quantification of peptidoforms in large sample cohorts by SWATH-MS. Nat Biotechnol. 2017 Aug;35(8):781-788. doi: 10.1038/nbt.3908. Epub 2017 Jun 12. PMID: 28604659
