---
layout: default
title: Allele Specific Read Counting of Variants
parent: Example Input Processing
grand_parent: Home
nav_order: 3
---
# Allele Specific Read Counting of Variants

Pileups or read counting for variants are obtained using [GATK's ASEReadCounter tool](https://gatk.broadinstitute.org/hc/en-us/articles/360037428291-ASEReadCounter). Using the lists of variants obtained as show in [Extracting Variant Information](extractvariant.md), a .vcf is created and indexed using a dna and rna reference (necessary input format for ASEReadCounter).

```bash
bcftools convert -c ID,CHROM,POS,AA -s $sample_id -f $reference --tsv2vcf $variantsTab -Ov -o $outfile_vcf

gatk IndexFeatureFile -F {outfile}
```

Pileups are performed the same for DNA and RNA read counting, with the only difference being the reference file used.

*Note: it is important to ensure that the reference bams are aligned to the same version of the genome to ensure compatibility between DNA and RNA counts.*

```bash
gatk ASEReadCounter --reference $ref_fasta --output $output -I $alignment_bam -variant $outfile_vcf --min-mapping-quality 10 --min-base-quality 2
```

The resulting output file can then be used as input for RNAmp