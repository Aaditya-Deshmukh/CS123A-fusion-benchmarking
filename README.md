# CS123A Long-Read Gene Fusion Benchmarking

This repository contains my final project for CS123A, benchmarking two gene fusion
detection tools (LongGF and Genion) on multiple long-read sequencing datasets using
mapping via minimap2 and timing/memory profiling via `/usr/bin/time -v`.

---

## Repository Structure

logs/
minimap2/
longgf/
genion/

results/
tsvs/

COMMANDS.md → contains the actual commands I ran
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
- ONT simulated datasets (75 / 80 / 85 / 90 / 95)

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

- **COMMANDS.md**

---

## Author

Aaditya Deshmukh, Sharon Mathys, Thuy-Du Nguyen, Natasha Prabhoo 
San Jose State University 
Fall 2025

