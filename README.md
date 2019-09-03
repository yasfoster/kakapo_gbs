# Kakapo_GBS
Referenced-based SNP calling &amp; population genomics.

## Pre-processing of Illumina raw reads

#### Process_radtags to demultiplex and trim RE for each lane 

`process_radtags -p /home/yasmin.holden/uoo00080/projects/stacks/raw/lane1/ -b /home/yasmin.holden/uoo00080/projects/stacks/info/barcodes_lane1.txt -o /home/yasmin.holden/uoo00080/projects/stacks/final_ncbi_stacks_v2/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane1.oe `

`process_radtags -p /home/yasmin.holden/uoo00080/projects/stacks/raw/lane2/ -b /home/yasmin.holden/uoo00080/projects/stacks/info/barcodes_lane2.txt -o /home/yasmin.holden/uoo00080/projects/stacks/final_ncbi_stacks_v2/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane2.oe `

`process_radtags -p /home/yasmin.holden/uoo00080/projects/stacks/raw/lane3/ -b /home/yasmin.holden/uoo00080/projects/stacks/info/barcodes_lane3.txt -o /home/yasmin.holden/uoo00080/projects/stacks/final_ncbi_stacks_v2/ --renz_1 pstI --renz_2 mspI --inline_null -c -q -r &> process_radtags_test.lane3.oe `

#### CAT individuals that contain multiple lanes e.g.

` cat Jack_1.fq.gz Jack_2.fq.gz > Jack_all.fq.gz`

#### Trimgalore to remove illumina adapters, Q<20 and fastQC, followed by MultiQC

`trim_galore --quality 20 --fastqc $name.fq.gz`

`multiqc .`

#### Cutadapt to truncate

`cutadapt -m 72 -l 72 -j 2 -o $name"_final.fq.gz" $name"_trimmed.fq.gz"`







## VCFtools filtering


#### Remove sex chromosomes so downstream analyses performed on neutral autosomal SNPs
Z chromosome CM013763.1
W chromosome CM013773.1

#### Remove sex chr for unfiltered.vcf (straight outta STACKS)

`vcftools --vcf unfilt.vcf --not-chr CM013763.1 --not-chr CM013773.1 --recode`

#After filtering, kept 182 out of 182 Individuals
#After filtering, kept 56218 out of a possible 58466 Sites
#final: nosex_unfilt.vcf


#### VCF for KGD > Fgrm
#filter for max depth of 20, min depth 2

`vcftools --vcf nosex_unfilt.vcf --minDP 2 --maxDP 30 --recode`

#After filtering, kept 182 out of 182 Individuals
#After filtering, kept 56218 out of a possible 56218 Sites
#final maxdp30_mindp2.vcf


#### VCF for Fcoff, sMLH, RoH
#filtered for depth, and 25% missing data

`vcftools --vcf maxdp30_mindp2.vcf --max-missing 0.75 --recode`

#After filtering, kept 182 out of 182 Individuals
#After filtering, kept 12625 out of a possible 56218 Sites
#final maxdp30_mindp2_md75.vcf

