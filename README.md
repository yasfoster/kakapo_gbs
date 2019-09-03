# Kakapo_GBS
Referenced-based SNP calling &amp; population genomics.


### PURPOSE: remove sex chromosomes so downstream analyses performed on neutral autosomal SNPs
#Z CM013763.1
#W CM013773.1


### Remove sex chr for unfiltered.vcf (straight outta STACKS)

`vcftools --vcf unfilt.vcf --not-chr CM013763.1 --not-chr CM013773.1 --recode`
#After filtering, kept 182 out of 182 Individuals
#After filtering, kept 56218 out of a possible 58466 Sites
#final: nosex_unfilt.vcf

### VCF for KGD > Fgrm
#filter for max depth of 25

'vcftools --vcf nosex_unfilt.vcf --minDP 2 --maxDP 30 --recode'
#After filtering, kept 182 out of 182 Individuals
#After filtering, kept 56218 out of a possible 56218 Sites
#final maxdp30_mindp.vcf

### VCF for Fcoff, sMLH, RoH
#filtered for depth, and 25% missing data

'vcftools --vcf maxdp30_mindp2.vcf --max-missing 0.75 --recode'
#After filtering, kept 182 out of 182 Individuals
#After filtering, kept 12625 out of a possible 56218 Sites
#final maxdp30_mindp2_md75.vcf

