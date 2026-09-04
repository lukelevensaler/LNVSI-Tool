
# LNVSI Tool (Levensaler Neogastropod Venomic Similarity Index Tool)

A PyQt6-based GUI application for analyzing spectrophotometry data from Levensaler Immobilized Metal Chelate Affinity Labelling Assay (LIMCALA)-based marine Neogastropod venomic analysis with the SAP classical machine learning-based Levensaler Neogastropod Venomic Similarity Indexing (LNVSI) algorithm

## Features

- PyQt6 GUI
- Robust CSV import and JSON-based autosave
- Region-segmented spectral deconvolution (Voigt fitting, Gaussian fallback)
- Machine learning-powered similarity metrics
- FDR-corrected statistical testing
- Export results as CSV, PDF, or XLSX (to Downloads)
- Detailed logging and error handling

## File Structure

- `src/` — Main application code
- `assets/` — Images, stylesheets
- `requirements.txt` — Python dependencies

## Logging

- Logs and Autosave file can be found in the LNVSI Tool Utilities Directory the application creates upon usage in your "~" directory (home directory)

## Exported Results

- All exports (CSV, PDF, XLSX) are saved to your Downloads directory by default.
- The Percentage Similarity data is saved and displayed via the main.py UI to 4 decimal places no matter what (not significant figures).
- The p-values data is, conversely, saved and displayed with 6 significant figures (not just rote decimal places).

---

Created by Luke Levensaler, 2025

