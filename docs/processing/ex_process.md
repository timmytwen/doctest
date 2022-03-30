---
layout: default
title: Example Input Processing
parent: Home
nav_order: 2
has_children: true
---
# Example RNAmp Workflow and Preprocessing

### Requirements

Files used in this example and their respective format. The exact formating and structure will differ depending on the tools used for alignment, copy number calling etc., but this example will provide a sample workflow on how to process commonly used bioinformatics file format into a format useable for RNAmp input.

Table of inputs
| File                          | Format        |
|:------------------------------|:--------------|
| DNA alignment                 | .bam          |
| RNA alignment                 | .bam          |
| Annotated somatic variants    | .txt          |
| Annotated germline variants   | .vcf          |
| Copy number data              | .battenberg   |
| Purity and ploidy information | .txt          |
### Processing Steps

1. [Extracting Variant Information](extractvariant.md)
2. [Mapping Copy Number Data to Variants](mapcn.md)
3. [Allele Specific Read Counting of Variants](asecount.md)
