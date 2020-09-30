## VCFtools filtering

### Remove sex chromosomes so downstream analyses performed on neutral autosomal SNPs

Z chromosome CM013763.1

W chromosome CM013773.1


#### Initial filtering: remove sex chomosomes and filtered for minimum depth of 2 & maximum depth of 30, for unfiltered.vcf i.e. straight out of STACKS

`vcftools --vcf 1_unfilt.vcf --not-chr CM013763.1 --not-chr CM013773.1 --minDP 2 --maxDP 30 --recode`

***

##### After, --missing-indv to generate file reporting the missingness on a per-individual basis

`vcftools --vcf unfilt.vcf --missing-indv`

##### removed individuals with high missingness >70%

`vcftools --vcf filt_sexchr_minmaxdp.vcf --remove-indv <name> --remove-indv <name> --remove-indv <name> --recode`

***

#### Filter VCF for inbreeding estimates 
##### filtered allowing up to 20% missing data

`vcftools --vcf maxdp30_mindp2.vcf --max-missing 0.80 --recode`

After filtering, kept 12241 out of a possible 56218 Site

###### Final vcf: filtered_md80.vcf

***
