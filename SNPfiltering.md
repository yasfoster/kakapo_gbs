## VCFtools filtering

### Remove sex chromosomes so downstream analyses performed on autosomal SNPs

Kakapo Z chromosome: CM013763.1

https://www.ncbi.nlm.nih.gov/nuccore/CM013763.1/

Kakapo W chromosome: CM013773.1

https://www.ncbi.nlm.nih.gov/nuccore/CM013773.1

***

#### Initial VCFtools v1.14 filtering: remove sex chomosomes and filtered for minimum depth of 2 & maximum depth of 30
##### Input files: variant call format (.vcf)

`vcftools --vcf 1_unfilt.vcf --not-chr CM013763.1 --not-chr CM013773.1 --minDP 2 --maxDP 30 --recode`

For more details on VCFtools usage, see:
https://vcftools.github.io/man_latest.html

***

##### After, --missing-indv to generate file reporting the missingness on a per-individual basis

`vcftools --vcf unfilt.vcf --missing-indv`

##### Removed individuals with high missingness >70%

`vcftools --vcf filt_sexchr_minmaxdp.vcf --remove-indv <name> --remove-indv <name> --remove-indv <name> --recode`

***

#### Filter VCF for inbreeding estimates 
##### Filtered allowing up to 20% missing data

`vcftools --vcf maxdp30_mindp2.vcf --max-missing 0.80 --recode`

***
