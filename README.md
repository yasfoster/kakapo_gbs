# Kakapo_GBS
Referenced-based SNP calling &amp; population genomics.



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



## Alignment to reference genome

#### BWA aligner

`bwa index jane_genome.fna`

`bwa mem -t 2 jane_genome.fna $name.fq.gz > $name.sam`

#### Samtools to convert SAM to BAM, sort BAM, and alignment statistics

`samtools view --threads 2 -b $name.sam > $name.bam`

`samtools sort $name.bam > $name"_sorted.bam"`

`samtools flagstat $name.bam > $name.txt`



## ref_map.pl

#### with reduced soft clipping and no population filters

`ref_map.pl -b 1 -T 8 -o /stacks/stacks_final/ -O /stacks/info/popmap.txt --samples /stacks/stacks_final/alignments_bwa/ -S -X "pstacks:--max_clipped 0.5" -X "populations:--fstats -k --log_fst_comp --vcf --vcf_haplotypes --genepop --plink --verbose"`



## VCFtools filtering

#### Remove sex chromosomes so downstream analyses performed on neutral autosomal SNPs
Z chromosome CM013763.1
W chromosome CM013773.1

#### Remove sex chr for unfiltered.vcf (straight outta STACKS)

`vcftools --vcf unfilt.vcf --not-chr CM013763.1 --not-chr CM013773.1 --recode`

After filtering, kept 182 out of 182 Individuals

After filtering, kept 56218 out of a possible 58466 Sites
final: nosex_unfilt.vcf


#### VCF for KGD > Fgrm
#filter for max depth of 20, min depth 2

`vcftools --vcf nosex_unfilt.vcf --minDP 2 --maxDP 30 --recode`

After filtering, kept 182 out of 182 Individuals

After filtering, kept 56218 out of a possible 56218 Sites
final maxdp30_mindp2.vcf


#### VCF for Fcoff, sMLH, RoH
#filtered for depth, and 25% missing data

`vcftools --vcf maxdp30_mindp2.vcf --max-missing 0.75 --recode`

After filtering, kept 182 out of 182 Individuals

After filtering, kept 12625 out of a possible 56218 Sites
final maxdp30_mindp2_md75.vcf

