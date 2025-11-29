# Exact Commands Used in This Project

This document contains the actual commands I ran step-by-step during the project
for mapping, fusion detection, and performance measurement.

---

## Minimap2 example

```bash
/usr/bin/time -v minimap2 -ax splice hg38.mmi SGNexRep1.fastq.gz \
  > SGNexRep1.sam \
  2> logs/minimap2/SGNexRep1_minimap.log

****## LongGf Example****
/usr/bin/time -v LongGF \
  SGNexRep1.sorted.bam \
  gencode.v22.annotation.gtf \
  100 50 100 0 0 2 0 \
  > results/tsvs/SGNexRep1_longgf.tsv \
  2> logs/longgf/SGNexRep1_longgf.log
/usr/bin/time -v genion \
  -i SGNexRep1.fastq.gz \
  --gtf Homo_sapiens.GRCh38.115.gtf \
  --gpaf SGNexRep1.paf \
  -s cdna.selfalign.tsv \
  -d genomicSuperDups.txt \
  -o results/tsvs/SGNexRep1_genion.tsv \
  2> logs/genion/SGNexRep1_genion.log
