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

## 2.1 Installing LongGF (C++ Source Build)

LongGF is implemented in C++ and was downloaded from its github page:

```bash
git clone https://github.com/WGLab/LongGF.git
cd LongGF
make
```
This required the system to have:
- `gcc`/`g++`
- `make`



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
