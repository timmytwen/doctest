---
layout: default
title: Example Input Processing
parent: Home
nav_order: 2
has_children: true
---
# Example RNAmp Workflow and Preprocessing

### Requirements

- DNA alignment file (.bam)
- RNA alignment file (.bam)
- Annotated somatic variants file (.vcf)
- Annotated germline variants file (.vcf)
- Copy number data
- Purity and ploidy information

### Example Processing

1. [Extracting Variant Information](docs\processing\extractvariant.md)
2. [Mapping Copy Number Data to Variants](docs\processing\mapcn.md)
3. [Allele Specific Read Counting of Variants](docs\processing\asecount.md)
