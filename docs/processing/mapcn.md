---
layout: default
title: Mapping Copy Number Data to Variants
parent: Example Input Processing
grand_parent: Home
nav_order: 2
---
# Mapping Copy Number Data to Variants

This examples shows the process of mapping copy number data to each variant, i.e. the major and minor allele counts. Using [copy number segments](ex_processing_data/example.cnsegments.csv) from [battenberg](https://github.com/Wedge-lab/battenberg). This segments file can be searched with a binary search:

```python
#python
from collections import defaultdict
import os
def read_cnv(sample, caveman_in, segments_out):
    '''
    read in caveman copy number file, extract information and reshape for RNAmp
    return copy number information as dictionary for cnv mapping
    '''
    segments = []
    segments = defaultdict(list)
    with open(caveman_in) as cnv:
        with open(segments_out, "w") as segments_out:
            segments_out.write("sample\tchr\tstartpos\tendpos\tnMajor\tnMinor\n")
            for line in cnv:
                if line.strip():
                    line = line.strip()
                    fields = line.split(',')
                    chromosome = fields[1]
                    start = int(fields[2])
                    end = int(fields[3])
                    normCN = int(fields[4])
                    minCN = int(fields[7])
                    majCN = int(fields[6]) - minCN
                    interval = [start, end]
                    cn = [majCN, minCN, normCN]

                    #write segments to new file in different format for RNAmp
                    segments_out.write('\t'.join([sample, chromosome, fields[2], fields[3], str(majCN), str(minCN)]) + "\n")

                    segments[chromosome].append([interval, cn])
    return segments

def binary_search(segments, pos):
    '''
    searches chromosomal positions in segments dict from read_cnv()
    '''
    n = len(segments)
    low = 0
    high = n - 1

    # Binary search
    while (low <= high):

        # Find the mid position
        mid = (low + high) >> 1

        # If position is found
        if (pos >= segments[mid][0][0] and pos <= segments[mid][0][1]):
            return mid

        # Check in first half
        elif (pos < segments[mid][0][0]):
            high = mid - 1

        # Check in second half
        else:
            low = mid + 1

    # Not found
    return -1
```

And this can be mapped to variants filtered and extracted from [extract variants](extractvariant.md):

```python
def read_annovar_snp(ssm_file, segments, gender):
    '''
    read annovar annotated snp variants file
    '''
    somatic_variants = []
    with open(ssm_file, "r") as snv:
        next(snv) #skip header
        for line in snv:
            if line.strip():
                fields = line.split()
                if fields[5] != "intergenic":
                    if fields[5] == "exonic":
                        ann = fields[7]
                    else:
                        ann = fields[5]
                    
                    id = "SOMATIC"
                    chrom = fields[0]
                    pos = int(fields[1])
                    #join ref alt into single string
                    aa = fields[3] + fields[4]

                    #for sorting
                    if chrom == 'X':
                        chroms = 23
                    elif chrom == 'Y':
                        chroms = 24 
                    else:
                        chroms = int(chrom)

                    majcn = 1
                    mincn = 1
                    normcn = 2

                    if (gender.casefold() == "male" and (chrom == 'X' or chrom == 'Y')):
                        normcn = 1

                    #search cnv for varaint
                    index = binary_search(segments[chrom], pos)

                    if (index != -1): #if variant is found in cnv
                        majcn = segments[chrom][index][1][0]
                        mincn = segments[chrom][index][1][1]
                        normcn = segments[chrom][index][1][2]

                    #differnt chrom columns for different reference in rna and dna bams
                    somatic_variants.append([id, chrom, pos, aa, ann, majcn, mincn, normcn, chroms])
    return somatic_variants

```



