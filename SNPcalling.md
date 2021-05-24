# Kakapo GBS
Referenced-based SNP calling for *Strigops habroptilus*

***

## Pre-processing of Illumina raw reads

#### Process_radtags (Stacks v1.46) to demultiplex raw reads 

`process_radtags -p /stacks/raw/lane1/ -b /stacks/info/barcodes_lane1.txt -o /stacks/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane1.oe `

`process_radtags -p /stacks/raw/lane2/ -b /stacks/info/barcodes_lane2.txt -o /stacks/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane2.oe `

`process_radtags -p /stacks/raw/lane3/ -b stacks/info/barcodes_lane3.txt -o /stacks/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane3.oe `


#### Trimgalore v0.4.5, a wrapper that removes Illumina adapters, removes low quality bases (Q<20), and runs fastQC for quality-control information (checks for adapter contamination etc.)

`trim_galore --quality 20 --fastqc $name.fq.gz`


#### Cutadapt v2.3 to truncate (trim reads to same length for Stacks input) while optimising number of reads written: run on a subset to see which trimming length keeps the most number of reads

`cutadapt -m 72 -l 72 -j 2 -o $name"_final.fq.gz" $name"_trimmed.fq.gz"`


#### FastQC and MultiQC v1.5 to collate quality-control information
`fastqc .`

`multiqc .`




***

## Alignment to reference genome, NCBI: GCA_004027225.1

#### Burrow Wheelers Aligner (BWA mem) v0.7.15 to map reads to reference genome
#### Index reference genome

`bwa index jane_genome.fna`

#### Alignment of indexed reference genome and GBS reads
`bwa mem -t 2 jane_genome.fna $name.fq.gz > $name.sam`

## Samtools v.1.8 

#### Convert SAM to BAM file format
`samtools view --threads 2 -b $name.sam > $name.bam`

#### Sort BAM files
`samtools sort $name.bam > $name"_sorted.bam"`

#### Alignment statistics
`samtools flagstat $name.bam > $name.txt`




***

## Stacks v.1.46 ref_map.pl

#### Referenced-based SNP calling with reduced soft clipping and no population filters except for write single SNP per locus, and output VCF and PLINK files

`ref_map.pl -b 1 -T 8 -o /stacks/ -O /stacks/info/popmap.txt --samples /stacks/alignments_bwa/ -S -X "pstacks:--max_clipped 0.5" -X "populations:-p 1 --write_single_snp --vcf --plink --verbose"`

***
