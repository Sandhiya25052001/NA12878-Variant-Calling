# NA12878-Variant-Calling
Variant calling analysis of the NA12878 human genome using NGS data including quality control, read alignment, and variant detection using BWA, SAMtools, and BCFtools.
# NA12878 Variant Calling Analysis

## Introduction

This project performs **variant calling analysis on the NA12878 human reference genome sample** using next-generation sequencing (NGS) data. The goal is to identify genomic variations such as **single nucleotide polymorphisms (SNPs)** and **insertions/deletions (indels)** relative to the human reference genome.

The workflow includes:

1. Quality control of raw sequencing reads
2. Genome alignment
3. BAM processing
4. Variant calling
5. Variant statistics analysis

---

# Dataset: NA12878 – Human Whole Genome Variant Calling

## Sample Details

| Property             | Value                       |
| -------------------- | --------------------------- |
| Sample               | NA12878                     |
| Organism             | Homo sapiens                |
| Data Source          | Sequence Read Archive (SRA) |
| Sequencing Platform  | Illumina                    |
| Read Type            | Paired-end                  |
| Read Length          | 101 bp                      |
| Total Reads per File | 92,459,454                  |
| Total Bases          | 9.3 Gbp                     |
| GC Content           | 39%                         |

The NA12878 genome is a **widely used benchmark dataset for validating variant calling pipelines**.

---

# Variant Calling Pipeline

The analysis pipeline used in this project:

```
Raw FASTQ
   │
   ▼
Quality Control (FastQC)
   │
   ▼
Genome Alignment (BWA)
   │
   ▼
BAM Processing (SAMtools)
   │
   ▼
Variant Calling (BCFtools)
   │
   ▼
Variant Statistics (bcftools stats)
```

---

# 1. Quality Control

Quality control of raw sequencing reads was performed using **FastQC**.

## FastQC Command

```bash
fastqc SRR622461_1.fastq.gz
fastqc SRR622461_2.fastq.gz
```

## FastQC Summary

Both sequencing files showed consistent characteristics:

* **Read length:** 101 bp
* **Total reads:** ~92.4 million reads per file
* **GC content:** ~39%
* **Sequencing platform:** Illumina

These values indicate **sufficient sequencing depth and high sequencing quality**.

### Per Base Sequence Quality

* Most bases have **Phred scores above Q30**
* Quality remains stable across read length

This indicates **very low sequencing error rates**.

### GC Content

The GC content (~39%) matches the expected distribution for the human genome, indicating **no significant GC bias**.

### Sequence Length Distribution

All reads have a **uniform length of 101 bp**, confirming a fixed-length Illumina sequencing protocol.

### Duplication Levels

Sequence duplication levels were low, suggesting **good library complexity and minimal PCR bias**.

---

# 2. Genome Alignment

Sequencing reads were aligned to the **human reference genome (hg38)** using **BWA-MEM**.

## Index Reference Genome

```bash
bwa index hg38.fa
```

## Alignment Command

```bash
bwa mem hg38.fa SRR622461_1.fastq.gz SRR622461_2.fastq.gz > aligned.sam
```

---

# 3. BAM Processing

SAM files were converted into sorted BAM files using **SAMtools**.

## Convert SAM to BAM

```bash
samtools view -bS aligned.sam > aligned.bam
```

## Sort BAM File

```bash
samtools sort aligned.bam -o sorted.bam
```

## Index BAM File

```bash
samtools index sorted.bam
```

---

# Alignment Statistics

Alignment quality was evaluated using **SAMtools flagstat**.

```bash
samtools flagstat sorted.bam
```

| Metric                | Value       |
| --------------------- | ----------- |
| Total reads           | 185,243,697 |
| Mapped reads          | 175,739,512 |
| Mapping rate          | 94.87%      |
| Properly paired reads | 169,148,744 |
| Properly paired rate  | 91.47%      |
| Singleton reads       | 2,244,391   |

### Interpretation

* **94.87% of reads mapped to the reference genome**, indicating excellent alignment quality.
* **91.47% properly paired reads** confirms correct paired-end sequencing.
* Low singleton reads indicate **minimal sequencing artifacts**.

---

# 4. Variant Calling

Variants were detected using **BCFtools**.

## Generate BCF File

```bash
bcftools mpileup -Ou -f hg38.fa sorted.bam | bcftools call -mv -Ob -o variants.bcf
```

## Convert BCF to VCF

```bash
bcftools view variants.bcf > raw_variants.vcf
```

---

# 5. Variant Statistics

Variant statistics were generated using **bcftools stats**.

```bash
bcftools stats raw_variants.vcf > variant_stats.txt
```

## Variant Summary

| Metric                | Value     |
| --------------------- | --------- |
| Number of samples     | 1         |
| Total variant records | 3,392,351 |
| SNPs                  | 2,965,921 |
| Indels                | 426,997   |
| Multiallelic sites    | 7,735     |

---

# SNP and Indel Distribution

The majority of detected variants are **SNPs**, which is expected for human genomes.

Typical human genome variation:

* **~3–4 million SNPs**
* **~400k–700k indels**

The detected values fall within this range, confirming **biologically realistic variant detection**.

---

# Transition / Transversion Ratio

| Metric        | Value     |
| ------------- | --------- |
| Transitions   | 1,911,708 |
| Transversions | 1,054,999 |
| Ts/Tv Ratio   | 1.81      |

### Interpretation

The **Ts/Tv ratio of 1.81** closely matches the expected value (~1.8) for whole genome sequencing.

This indicates:

* High accuracy of variant detection
* Low false-positive variant calls

---

# Variant Quality Scores

Most variants have **quality scores between 30 and 100**.

| QUAL Score | Interpretation               |
| ---------- | ---------------------------- |
| >30        | High-confidence variant      |
| >50        | Very high-confidence variant |

High QUAL values indicate strong support from sequencing reads.

---

# Overall Pipeline Evaluation

| Step               | Result                | Assessment |
| ------------------ | --------------------- | ---------- |
| Sequencing Quality | High                  | Passed     |
| Alignment          | 94.87% mapped         | Excellent  |
| Variant Detection  | 3.39 million variants | Expected   |
| Ts/Tv Ratio        | 1.81                  | Accurate   |
| Variant Quality    | High                  | Reliable   |

---

# Conclusion

The NA12878 whole genome sequencing dataset demonstrated **high sequencing quality, efficient genome alignment, and reliable variant detection**.

Key findings include:

* Identification of **3.39 million genomic variants**
* **2.96 million SNPs and 426,997 indels**
* **94.87% mapping rate**
* **Ts/Tv ratio of 1.81**

These results are consistent with known human genome variation and confirm the reliability of the variant calling pipeline.

---

# Tools Used

* FastQC
* BWA
* SAMtools
* BCFtools

---

# Author

Sandhiya Prabhakar

MSc Bioinformatics
