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





METHODS.md → contains the methods/commands used
README.md → description of the project
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

## Authors

Aaditya Deshmukh, Sharon Mathys, Thuy-Du Nguyen, Natasha Prabhoo 
San Jose State University 
Fall 2025

