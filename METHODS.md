# METHODS

This document describes the full methodology used to install the required environments, prepare data, run fusion detection software, and measure runtime and memory performance for each dataset and tool. The workflow includes running on WSL Ubuntu, installing necessary bioinformatics tools, mapping reads using minimap2, generating BAM or PAF files, and executing LongGF and Genion with performance profiling using `/usr/bin/time -v`.

---

# 1. Computational Environment Setup

## 1.1 WSL + Ubuntu  
I used **Windows Subsystem for Linux (WSL)** to install Ubuntu inside Windows.  
This allowed me to have a real Linux environment where bioinformatics tools are natively supported.

Linux was chosen specifically because:

- it supports the **`/usr/bin/time -v`** command
- compilation & execution of tools like LongGF and minimap2 are easier and faster
- file path handling in tools is standardized

WSL was installed by opening PowerShell as Admin:

```bash
wsl --install
```
Reboot -> open Ubuntu -> run

Then Ubuntu was updated & build tools were installed:

```bash
sudo apt update
sudo apt install build-essential git time htop
```

# 2. Installation of BioInformatics Tools

All tools used in this project were installed inside of conda environments for dependency management, reproducibility, and version control

Two Environments were created:

- `fusionenv` : for LongGF C++ build (also contains minimap2 & samtools)
- `genionenv` : for Genion (also contains minimap2)

 ---


## 2.1 Installing minimap2 and samtools (conda)

First the dependencies need to be installed since this is run with python

```bash
sudo apt install -y build-essential git python3 python3-pip zlib1g-dev libbz2-dev liblzma-dev
```
Then we need to install samtools and Minimap2

```bash
sudo apt install -y samtools
sudo apt install -y minimap2
```


## 2.2 Installing Genion

Genion is packaged for conda, which makes installation easier

First we need to install conda:

```bash
sudo apt install -y wget
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```
Restart Ubuntu then run:

```bash
source ~/.bashrc
```

Then we need to add the bioconda channels:

```bash
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```
After we need to create a dedicated environment for Genion:

```bash
conda create -n genionenv python=3.8
# activate it
conda activate genionenv
# install it here
conda install genion
```
## 2.3 Setting up the `fusionenv` Conda Environment for LongGF

To run LongGF and the supporting alignment tools, I created a dedicated conda
environment called `fusionenv`. This environment was designed to contain LongGF
(as a compiled C++ binary), minimap2, samtools, and their required dependencies
without conflict from other installed tools.

```bash
conda create -n fusionenv python=3.10 -y
conda activate fusionenv
conda install -c bioconda minimap2 samtools -y
```
As LongGF is implemented in C++, it was not installed through conda or pip.
Instead, it was cloned and compiled from source inside the `fusionenv`

```bash
git clone https://github.com/WGLab/LongGF.git
cd LongGF
make
```

This produced a direct executable within the environment, wihch enabled direct execution using:

`LongGF <args>`

# 3. Reference Genome Usage and Annotation Differences

Two different genome builds and GTF annotation files were used depending on dataset type.

## 3.1 SGNexRep datasets (biological datasets)

- Genome index: **GRCh38.mmi**
- Annotation file: **Homo_sapiens.GRCh38.115.gtf** (Ensembl)
- Used for: SGNexRep1, SGNexRep2

These datasets ran correctly with Ensembl-based annotations mapping to GRCh38.

---

## 3.2 ONT simulated datasets (synthetic datasets: 75/80/85/90/95)

- Genome index: **hg38.mmi**
- Annotation file: **gencode.v22.annotation.gtf** (GENCODE)
- Used for: ONT75, ONT80, ONT85, ONT90, ONT95

Simulated reads required GENCODE formatting and hg38-based genome indexing.

---

## 3.3 Consequence of incorrect pairing

Using mismatched genome/annotation pairs resulted in:

- zero fusion predictions
- unmapped read IDs  
- transcript ID mismatches  
- incorrect chromosome positions  
- failures in fusion detection

This required careful dataset-specific reference selection.

---

# 4. Full LongGF Execution Workflow (End-to-End)

LongGF uses BAM alignment files and requires name-sorted BAM input.

## 4.1 Mapping FASTQ → SAM using minimap2

For **SGNex datasets (GRCh38)**:

```bash
/usr/bin/time -v minimap2 -ax splice GRCh38.mmi SGNexRep1.fastq.gz \
    > SGNexRep1.sam \
    2> logs/minimap2/SGNexRep1_minimap.log
```
For **ONT datasets(hg38)**:
```bash
/usr/bin/time -v minimap2 -ax splice hg38.mmi ONT75.fastq.gz \
    > ONT75.sam \
    2> logs/minimap2/ONT75_minimap.log
```

## 4.2 Converting SAM -> BAM

```bash
samtools view -bS dataset.sam > dataset.bam
```

## 4.3 Name-sorting BAM for LongGF

```bash
samtools sort -n dataset.bam -o dataset.sorted.bam
```

This is required for LongGF

## 4.4 Running LongGF

For **SGNex datasets (GRCh38)**:

```bash
/usr/bin/time -v LongGF \
    SGNexRep1.sorted.bam \
    Homo_sapiens.GRCh38.115.gtf \
    100 50 100 0 0 2 0 \
    > results/tsvs/SGNexRep1_longgf.tsv \
    2> logs/longgf/SGNexRep1_longgf.log
```

For **ONT datasets(hg38)**:

```bash
/usr/bin/time -v LongGF \
    ONT75.sorted.bam \
    gencode.v22.annotation.gtf \
    100 50 100 0 0 2 0 \
    > results/tsvs/ONT75_longgf.tsv \
    2> logs/longgf/ONT75_longgf.log
```

# 5 Full Genion Execution Workflow (End-to-End)

Genion uses FASTQ + PAF files

## 5.1 Mapping FASTQ -> PAF using minimap2

For **SGNex datasets (GRCh38)**:

```bash
/usr/bin/time -v minimap2 -x splice -c GRCh38.mmi SGNexRep1.fastq.gz \
    > SGNexRep1.paf \
    2> logs/minimap2/SGNexRep1_minimap_paf.log
```

For **ONT datasets(hg38)**:

```bash
/usr/bin/time -v minimap2 -x splice -c hg38.mmi ONT75.fastq.gz \
    > ONT75.paf \
    2> logs/minimap2/ONT75_minimap_paf.log
```

## 5.2 Running Genion

For **SGNex datasets (GRCh38)**

```bash
/usr/bin/time -v genion \
    -i SGNexRep1.fastq.gz \
    --gtf Homo_sapiens.GRCh38.115.gtf \
    --gpaf SGNexRep1.paf \
    -s cdna.selfalign.tsv \
    -d genomicSuperDups.txt \
    -o results/tsvs/SGNexRep1_genion.tsv \
    2> logs/genion/SGNexRep1_genion.log
```

For **ONT datasets(hg38)**:

```bash
/usr/bin/time -v genion \
    -i ONT75.fastq.gz \
    --gtf gencode.v22.annotation.gtf \
    --gpaf ONT75.paf \
    -s cdna.selfalign.tsv \
    -d genomicSuperDups.txt \
    -o results/tsvs/ONT75_genion.tsv \
    2> logs/genion/ONT75_genion.log
```

# 6. Timing and Resource Profiling

Every execution, FASTQ mapping, SAM/BAM converting, sorting, and fusion detection was tracked using: 
`/usr/bin/time -v <command>`

This allowed the collection of:

- Wall-clock time
- Maximum RAM
- %CPU utilization
- I/O statistics
  
Each output was stored in the appropriate:

- logs/minimap2/
- logs/longgf/
- logs/genion/

# 7. Example of Full Pipeline for One Dataset (SGNexRep1)

```bash
conda activate fusionenv
/usr/bin/time -v minimap2 -ax splice GRCh38.mmi SGNexRep1.fastq.gz > SGNexRep1.sam 2> logs/minimap2/SGNexRep1_minimap.log
samtools view -bS SGNexRep1.sam > SGNexRep1.bam
samtools sort -n SGNexRep1.bam -o SGNexRep1.sorted.bam
/usr/bin/time -v LongGF SGNexRep1.sorted.bam Homo_sapiens.GRCh38.115.gtf 100 50 100 0 0 2 0 > results/tsvs/SGNexRep1_longgf.tsv 2> logs/longgf/SGNexRep1_longgf.log

conda activate genionenv
/usr/bin/time -v minimap2 -x splice -c GRCh38.mmi SGNexRep1.fastq.gz > SGNexRep1.paf 2> logs/minimap2/SGNexRep1_minimap_paf.log
/usr/bin/time -v genion -i SGNexRep1.fastq.gz --gtf Homo_sapiens.GRCh38.115.gtf --gpaf SGNexRep1.paf -s cdna.selfalign.tsv -d genomicSuperDups.txt -o results/tsvs/SGNexRep1_genion.tsv 2> logs/genion/SGNexRep1_genion.log
```

# 8. Summary of LongGF vs Genion Input Requirements

| Tool    | Required Input   | Genome Used        | Annotation Used              |
|----------|------------------|--------------------|------------------------------|
| LongGF   | Name-sorted BAM  | GRCh38 or hg38     | Homo_sapiens.GRCh38.115.gtf or gencode.v22.annotation.gtf |
| Genion   | FASTQ + PAF      | GRCh38 or hg38     | Homo_sapiens.GRCh38.115.gtf or gencode.v22.annotation.gtf |

# 9. Fusion Result Output Files (TSV)

Each fusion detection run produced a TSV output file containing the list of gene fusion predictions detected in that dataset. These TSVs represent the final outputs of LongGF and Genion and were collected and are included in this repository for reference and evaluation.

All resulting TSV files are located in:

**results/tsvs/**

Examples include:

- `SGNexRep1_fusions.tsv`: LongGF tsv file
- `SGNexRep1_genion.tsv`: Genion tsv file
- `ONT75_SumGF.tsv`: LongGF tsv file
- `ONT75_genion.tsv`: Genion tsv file


The TSV files contain the fusion predictions generated during the analysis for each dataset. Since the output formats can vary between tools and datasets (particularly between biological SGNex samples and simulated ONT reads), the TSV files are provided “as produced” by the tools or lightly cleaned for readability.

Anyone reviewing the repository can directly inspect:

- what fusion events were detected  
- how many were found per dataset  
- which ones were found by both tools vs. only one tool  

These result files form the basis for evaluating fusion detection accuracy and comparing performance between LongGF and Genion.

# 10. Hardware Specifications of Computation Environment

All gene-fusion analyses were executed on a local laptop computer with the following specifications:

**Host Machine (Windows):**
- Device name: ALaptop
- CPU: AMD Ryzen 7 PRO 7840U  
  - 8 cores / 16 threads  
  - Base clock: 3.30 GHz  
  - Integrated GPU: Radeon 780M (not used for computation)
- Installed RAM: 32 GB (30.7 GB usable)
- System type: 64-bit OS, x64-based processor
- Storage: SSD (NVMe) — supports fast read/write for BAM/PAF processing

This hardware allowed both LongGF and Genion to be executed efficiently on-device without requiring an HPC cluster or remote compute instance.

**Linux Runtime Environment (inside WSL2):**
- WSL2 virtualized Linux kernel
- Ubuntu (inside WSL)
- Shared memory and CPU resources with Windows host
- Full support for `/usr/bin/time -v` for resource profiling

Notes on performance:
- Multithreading allowed minimap2 to efficiently align large FASTQ datasets
- Sufficient RAM (32GB) prevented memory starvation during large-file operations
- SSD ensured rapid sorting and indexing of BAM files

No GPU acceleration was used, all workloads were executed on CPU.

