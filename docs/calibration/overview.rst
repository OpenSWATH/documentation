Calibration in OpenSwathWorkflow
========================

The calibration step in OpenSwathWorkflow ensures that retention time (RT), mass-to-charge (m/z), and ion mobility (IM) are placed on a consistent and accurate scale before targeted extraction and scoring.

Retention Time (RT) normalization:
Using spiked-in iRT peptides, OpenSWATH builds a regression model between observed RTs and expected iRT values. This corrects for run-to-run variation in chromatographic performance and aligns peptide features onto a standardized RT axis.

m/z calibration:
High-accuracy precursor and fragment m/z values are critical for targeted extraction. By comparing observed m/z values of calibrant peptides to their theoretical masses, systematic offsets are detected and corrected, improving extraction specificity.

Ion mobility calibration (for timsTOF / PASEF data):
Similar to RT and m/z, ion mobility values can drift or be offset between runs. Calibrating IM with known peptide references ensures that extraction windows are properly centered, maximizing sensitivity while reducing interferences.

Why it matters:
Calibration reduces systematic bias in all three dimensions (RT, m/z, IM), leading to more accurate peak picking, higher specificity in fragment extraction, and better comparability across runs. Without calibration, peptide features may fall outside extraction windows or align poorly across datasets, reducing identification and quantification quality.