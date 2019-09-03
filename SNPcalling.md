# Kakapo_GBS
Referenced-based SNP calling for *Strigops habroptilus*

***

## Pre-processing of Illumina raw reads

#### Process_radtags to demultiplex and trim RE for each lane 

`process_radtags -p /stacks/raw/lane1/ -b /stacks/info/barcodes_lane1.txt -o /stacks/final_ncbi_stacks_v2/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane1.oe `

`process_radtags -p /stacks/raw/lane2/ -b /stacks/info/barcodes_lane2.txt -o /stacks/final_ncbi_stacks_v2/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane2.oe `

`process_radtags -p /stacks/raw/lane3/ -b stacks/info/barcodes_lane3.txt -o /stacks/final_ncbi_stacks_v2/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane3.oe `


#### CAT individuals that contain multiple lanes

` cat Jack_1.fq.gz Jack_2.fq.gz > Jack_all.fq.gz`


#### Trimgalore to remove illumina adapters, Q<20 and fastQC, followed by MultiQC

`trim_galore --quality 20 --fastqc $name.fq.gz`

`multiqc .`


#### Cutadapt to truncate

`cutadapt -m 72 -l 72 -j 2 -o $name"_final.fq.gz" $name"_trimmed.fq.gz"`


#### Final fastQC and MultiQC
`fastqc .`
`multiqc .`

***

## Alignment to reference genome

#### BWA aligner

`bwa index jane_genome.fna`

`bwa mem -t 2 jane_genome.fna $name.fq.gz > $name.sam`

#### Samtools to convert SAM to BAM, sort BAM, and alignment statistics

`samtools view --threads 2 -b $name.sam > $name.bam`

`samtools sort $name.bam > $name"_sorted.bam"`

`samtools flagstat $name.bam > $name.txt`

***

## Stacks V.1.46 ref_map.pl

#### with reduced soft clipping and no population filters

`ref_map.pl -b 1 -T 8 -o /stacks/stacks_final/ -O /stacks/info/popmap.txt --samples /stacks/stacks_final/alignments_bwa/ -S -X "pstacks:--max_clipped 0.5" -X "populations:--fstats -k --log_fst_comp --vcf --vcf_haplotypes --genepop --plink --verbose"`

***
