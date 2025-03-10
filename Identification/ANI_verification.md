# Average Nucleotide Identity (ANI) Analysis Guide

## Introduction
Average Nucleotide Identity (ANI) is a measure of nucleotide-level genomic similarity between bacterial genomes. This guide covers the use of FastANI and related tools for calculating and visualizing ANI values.

## FastANI Setup and Usage

### Installation

```bash
# Via conda (recommended)
conda create -n ani_tools
conda activate ani_tools
conda install -c bioconda fastani

# Via source
git clone https://github.com/ParBLiSS/FastANI.git
cd FastANI
make

# Verify installation
fastANI --version
```

### Preparing Reference Data

#### Download RefSeq Data
```bash
# Download RefSeq assembly summary
wget https://ftp.ncbi.nlm.nih.gov/genomes/refseq/bacteria/assembly_summary.txt

# Filter complete genomes
awk -F "\t" '$12=="Complete Genome" {print $20}' assembly_summary.txt > complete_genomes.txt

# Download genomes
while read url; do
    wget "$url"/*.fna.gz
done < complete_genomes.txt

# Decompress
gunzip *.fna.gz
```

#### Create Reference List
```bash
# Create list of reference genomes
ls *.fna > reference_list.txt

# For multiple queries
ls query_*.fasta > query_list.txt
```

### Running FastANI

#### Basic Usage
```bash
# Single query vs single reference
fastANI \
    -q contigs.fasta \
    -r reference.fasta \
    -o output.txt

# Single query vs multiple references
fastANI \
    -q contigs.fasta \
    --rl reference_list.txt \
    -o multi_ref_output.txt

# Multiple queries vs multiple references
fastANI \
    --ql query_list.txt \
    --rl reference_list.txt \
    -o all_vs_all.txt
```

#### Advanced Options
```bash
# Adjust fragment size and minimum matches
fastANI \
    -q contigs.fasta \
    -r reference.fasta \
    --fragLen 1000 \
    --minFraction 0.5 \
    -o detailed_output.txt

# Use multiple threads
fastANI \
    -q contigs.fasta \
    -r reference.fasta \
    --threads 8 \
    -o threaded_output.txt
```

### Output Format
FastANI output contains:
```plaintext
query_genome  reference_genome  ANI_value  fragments_mapped  total_fragments
```

## Visualization with Proksee

### Overview
**Website**: [Proksee](https://proksee.ca/)

#### Features
* Interactive visualization
* Genome comparison
* Synteny analysis
* Gene annotation
* Metabolic pathway mapping

#### Usage Steps
1. Register/Login to Proksee
2. Upload genome files:
   * Query genome
   * Reference genome(s)
   * FastANI results
3. Select visualization options
4. Generate and download reports

#### Visualization Types
* Heatmaps
* Circular genome plots
* Linear comparison views
* Synteny blocks
* Gene organization

## Analysis and Interpretation

### ANI Thresholds
* **Species boundary**: ≥95% ANI
* **Genus boundary**: ≥75% ANI
* **Family boundary**: ≥60% ANI

### Quality Control
* Minimum genome completeness
* Appropriate fragment coverage
* Sufficient query length
* Contamination assessment

## Additional Analysis Tools

### 1. OrthoANI
Alternative to FastANI with different algorithm

```bash
# Installation
pip install orthoani
# Run OrthoANI
orthoani -q sequence1.fa -r sequence2.fa
```

## Best Practices

### Data Preparation
* Use complete or high-quality draft genomes
* Check assembly quality metrics
* Verify taxonomy assignments
* Use appropriate reference genomes

### Analysis Workflow
1. Quality check input genomes
2. Run FastANI with appropriate parameters
3. Validate results
4. Visualize relationships
5. Document findings

### Tips for Success
* Use multiple reference strains
* Consider evolutionary relationships
* Validate critical results
* Document analysis parameters

## Common Issues and Solutions

### Low ANI Values
* Check genome quality
* Verify taxonomic assignment
* Consider evolutionary distance
* Check for contamination

### Computation Issues
* Adjust fragment size
* Increase memory allocation
* Use multiple threads
* Split large datasets

## Results Interpretation

### Making Species-Level Decisions
* Consider multiple lines of evidence
* Use appropriate thresholds
* Account for biological context
* Document decision criteria

## Additional Resources

* [FastANI GitHub](https://github.com/ParBLiSS/FastANI)
* [ANI Calculator](https://www.ezbiocloud.net/tools/ani)
* [NCBI Genome](https://www.ncbi.nlm.nih.gov/genome)
* [Type Strain Genome Server](https://tygs.dsmz.de)
* [OrthoANI](https://github.com/althonos/orthoani)

## Tool Comparison

| Feature        | FastANI | OrthoANI |  Proksee  |
|----------------|---------|----------|-----------|
| Speed          | Fast    | Moderate |  N/A      |
| Accuracy       | High    | High     |  N/A      |
| Visualization  | No      | Basic    |  Advanced |
| Local Install  | Yes     | Yes      |  No       |
| Memory Usage   | Low     | Moderate |  N/A      |
| Batch Process  | Yes     | Limited  |  Limited  |

---

**Note**: Regular updates of reference databases and tools are essential for accurate analysis.