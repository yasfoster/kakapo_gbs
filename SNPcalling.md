# Kākāpō GBS
Referenced-based SNP calling for *Strigops habroptilus*

***

## Pre-processing of Illumina raw reads

#### Process_radtags (a module of Stacks v1.46) to demultiplex raw reads and trim barcodes. Provide restriction enzyme information to check for intact enzyme cutsites
##### Input files: raw sequence data (.fastq) and corresponding barcodes (.txt)

`process_radtags -p /stacks/raw/lane1/ -b /stacks/info/barcodes_lane1.txt -o /stacks/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane1.oe `

`process_radtags -p /stacks/raw/lane2/ -b /stacks/info/barcodes_lane2.txt -o /stacks/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane2.oe `

`process_radtags -p /stacks/raw/lane3/ -b stacks/info/barcodes_lane3.txt -o /stacks/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane3.oe `

For more details regarding input files and parameter choices, see:
https://catchenlab.life.illinois.edu/stacks/comp/process_radtags.php

***

#### Trimgalore v0.4.5, a wrapper that removes Illumina adapters, removes low quality bases (Q<20), and runs fastQC for quality-control information (checks for adapter contamination etc.)
##### Input files: demultiplexed reads (.fq)

`trim_galore --quality 20 --fastqc $name.fq.gz`

For more usage details, see:
https://github.com/FelixKrueger/TrimGalore/blob/master/Docs/Trim_Galore_User_Guide.md

***

#### Cutadapt v2.3 to truncate (trim reads to same length for Stacks input) while optimising number of reads written: run on a subset to see which trimming length keeps the most number of reads and take the average 
##### Input files: demultiplexed reads (.fq)

`cutadapt -m 72 -l 72 -j 2 -o $name"_final.fq.gz" $name"_trimmed.fq.gz"`

For more usage details, see:
https://cutadapt.readthedocs.io/en/stable/guide.html

***

#### FastQC and MultiQC v1.5 to collate quality-control information
##### Input files: cleaned reads (.fq)

`fastqc .`

`multiqc .`

For more usage details, see:

https://www.bioinformatics.babraham.ac.uk/projects/fastqc/

https://multiqc.info/docs/

***

## Alignment to kākāpō reference genome (Jane), NCBI: GCA_004027225.1

https://www.ncbi.nlm.nih.gov/assembly/GCF_004027225.1/

#### Burrow Wheelers Aligner (BWA mem) v0.7.15 to map reads to reference genome

For more usage details, see: https://github.com/lh3/bwa

#### Index reference genome
##### Input files: reference_genome.fna, downloaded from NCBI

`bwa index jane_genome.fna`

#### Alignment of indexed reference genome and GBS reads
##### Input files: reference_genome.fna, and keep index in same directy

`bwa mem -t 2 jane_genome.fna $name.fq.gz > $name.sam`

***

## Samtools v.1.8 

For more usage details, see: http://www.htslib.org/doc/samtools.html

For format specification, see: http://samtools.github.io/hts-specs/SAMv1.pdf

#### Convert SAM to BAM file format
##### Input files: sequence alignment/map files (.sam)

`samtools view --threads 2 -b $name.sam > $name.bam`

#### Sort BAM files
##### Input files: binary sequence alignment/map files files (.bam)

`samtools sort $name.bam > $name"_sorted.bam"`

#### Alignment statistics
##### Input files: binary sequence alignment/map files files (.bam)

`samtools flagstat $name.bam > $name.txt`


***

## Stacks v.1.46 ref_map.pl

#### Before running Stacks referenced-based pipeline (ref_map.pl), execute each component (e.g. gstacks) individually for at least a subset. 

For an excellent and informative protocol (for most steps mentioned on this page), see:

Rochette, N., Catchen, J. Deriving genotypes from RAD-seq short-read data using Stacks. Nat Protoc 12, 2640–2659 (2017). https://doi.org/10.1038/nprot.2017.123

#### Referenced-based SNP calling with reduced soft clipping and no population filters except for write single SNP per locus, and output VCF and PLINK files

`ref_map.pl -b 1 -T 8 -o /stacks/ -O /stacks/info/popmap.txt --samples /stacks/alignments_bwa/ -S -X "pstacks:--max_clipped 0.5" -X "populations:-p 1 --write_single_snp --vcf --plink --verbose"`

For more usage details, see: https://catchenlab.life.illinois.edu/stacks/manual-v1/#pipe

***
