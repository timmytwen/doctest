# rnamp
RNA Amplification in Mixed Primary Tumors

RNAmp calculates cancer cell specific transcriptional output using DNA and RNA sequencing data from primary tumors.

## Installation
```R
devtools::install_github('mjz1/rnamp')
```
[Basic Usage](usage.md)

## Example RNAmp Workflow and Preprocessing

### Requirements

- DNA alignment file (.bam)
- RNA alignment file (.bam)
- Annotated somatic variants file (.vcf)
- Annotated germline variants file (.vcf)
- Copy number data
- Purity and ploidy information
### Example Processing
- [Extracting Variant Information](extractvariant.md)
- [Mapping Copy Number Data to Variants](mapcn.md)
- [Allele Specific Read Counting of Variants](asecount.md)
