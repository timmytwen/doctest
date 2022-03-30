---
layout: default
title: Extracting Variant Information
parent: Example Input Processing
nav_order: 1
---
# Extracting Variant Information
Examples showing extracting variants from SnpEff annotated VCF and annovar annotations for rnamp.
## Variants from Variant Call Format (VCF) file
Germline variants (.vcf) were annotated previously annotated with [SnpEff](http://pcingola.github.io/SnpEff/). The annotated variants (`$germline_vcf`) is filtered for heterozygous SNPs with GQ > 30 in protein coding regions (`$exon_bed`). This is written to a new filtered .vcf from which variant information and annotation is extracted and written to a .tsv (`$germline_variants_tsv`)

```bash
bcftools view $germline_vcf | bcftools norm -m+ | bcftools view -m2 -M2 -v snps | \
    java -jar /hpf/tools/centos6/snpEff/4.3/SnpSift.jar intervals $exon_bed | \
    java -jar /hpf/tools/centos6/snpEff/4.3/SnpSift.jar filter "(isHet(GEN[0]) & (GEN[0].GQ > 30))" | \
    bcftools annotate --set-id 'GERMLINE' | bcftools sort -Oz > $filtered_vcf; tabix -p vcf $filtered_vcf

java -jar /hpf/tools/centos6/snpEff/4.3/SnpSift.jar extractFields -s "," $3 ID CHROM POS REF ALT ANN[*].EFFECT > $germline_variants_tsv

```

## Variants from ANNOVAR tab-delimited output file
Variants can be extracted from a .tsv using a simple python script to extract fields of interest and written to a new .tsv:

```python
somatic_variants = []
with open(ssm_file, "r") as snv:
    next(snv) #skip header
    for line in snv:
        if line.strip():
            fields = line.split()
            if fields[5] != "intergenic":
                if fields[5] == "exonic":
                    annotation = fields[7]
                else:
                    annotation = fields[5]
                
                id = "SOMATIC"
                chrom = fields[0]
                pos = int(fields[1])
                #join ref alt into single string
                aa = fields[3] + fields[4]

                somatic_variants.append([id, chrom, pos, aa, annotation])

with open("/hpf/largeprojects/adam/projects/timmy/RNAmp/kics/sample_data/variants.txt", "w") as f:
    f.write("ID\tCHROM\tPOS\tAA\tCLASSIFICATION\n")
    for i in somatic_variants:
        f.write('\t'.join(map(str, i[:-1])) + "\n") #slice last element used for sorting
```

### Tools used version info.
[bcftools v1.6](http://www.htslib.org/doc/1.6/bcftools.html)

[SnpSift v4.3](http://pcingola.github.io/SnpEff/)

