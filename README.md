# CS123A Long-Read Gene Fusion Benchmarking

This repository contains our final project for CS123A, benchmarking two gene fusion
detection tools (LongGF and Genion) on multiple long-read sequencing datasets using
mapping via minimap2 and timing/memory profiling.

---

## Repository Structure

**logs** : Contains all the time logs for the running times of the various datasets

>_minimap2_: Contains the time logs for all minimap2 commands

>_longgf_: Contains the time logs for all longGF commands

>_genion_: Contains all the time logs for all genion commands

**results** : Contains all the final .tsv files with the found fusions through either longGF or genion

**.gitignore** : Command used to ignore files during initial commit

**FLOWCHART.md** : Flowchart showing workflow pipeline

**METHODS.md** : Detailed report on the methods used for this project

**README.md** : Readme file for this github repo


---

## Tools Used

- WSL Ubuntu
- minimap2
- LongGF (C++)
- Genion (Python)
- samtools
- /usr/bin/time -v

---

## Datasets Evaluated

- SGNexRep1
- SGNexRep2
- ONT simulated error datasets (75 / 80 / 85 / 90 / 95)

---

## Purpose of the Project

- Detect gene fusions across datasets
- Measure runtime and memory cost of each tool
- Evaluate detection performance using:
  - TP (True Positives)
  - FP (False Positives)
  - FN (False Negatives)
  - Precision
  - Recall
  - F1 score

---

## Commands Used

The complete list of commands used for minimap2, LongGF, and Genion can be found in:

- **METHODS.md**

---

## References 

    X. Zhao et al., “Gene fusion detection in long-read transcriptome sequencing data with GFvoter,” BMC Genomics, vol. 26, no. 1, June 2025. doi:10.1186/s12864-025-11866-6 

    Y. Chen, et. al. “Gene Fusion Detection and Characterization in Long-Read Cancer Transcriptome Sequencing Data with FusionSeeker”, Cancer Research, vol 83, issue 1,  28-33, January 2023, doi:10.1158/0008-5472.can-22-1628 

    Y. Chen et. al., “A systematic benchmark of Nanopore long-read RNA sequencing for transcript-level analysis in human cell lines”, Nature Methods, vol. 22, 801–812 (2025). https://doi.org/10.1038/s41592-025-02623-4 
    The SG-NEx data was accessed on November 14, 2025 at registry.opendata.aws/sg-nex-data 

---

## Authors

Aaditya Deshmukh, Sharon Mathys, Thuy-Du Nguyen, Natasha Prabhoo 
San Jose State University 
Fall 2025

