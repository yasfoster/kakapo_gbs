## VCFtools filtering




##### After, --missing-indv to generate file reporting the missingness on a per-individual basis

`vcftools --vcf unfilt.vcf --missing-indv`

##### removed individuals with high missingness >75%

`vcftools --vcf unfilt.vcf --remove-indv Sarah --remove-indv Tiaho --remove-indv Weheruatanga --recode`

Final vcf: unfilt_removed_indv_high_miss.vcf

***

#### Remove sex chromosomes so downstream analyses performed on neutral autosomal SNPs

Z chromosome CM013763.1

W chromosome CM013773.1


#### Remove sex chr for unfiltered.vcf (straight outta STACKS, except removed Sarah, Tiaho & Wehetuatanga)

`vcftools --vcf unfilt_removed_indv_high_miss.vcf --not-chr CM013763.1 --not-chr CM013773.1 --recode`

After filtering, kept 179 out of 179 Individuals

After filtering, kept 56218 out of a possible 58466 Sites

Final vcf: nosex_unfilt.vcf

***

#### VCF for KGD > Fgrm
##### filter for max depth of 30, min depth 2

`vcftools --vcf nosex_unfilt.vcf --minDP 2 --maxDP 30 --recode`

After filtering, kept 179 out of 179 Individuals

After filtering, kept 56218 out of a possible 56218 Sites

Final vcf: KGD_maxdp30_mindp2.vcf

***

#### VCF for Fcoff, sMLH, RoH
##### filtered for depth, and 25% missing data

`vcftools --vcf maxdp30_mindp2.vcf --max-missing 0.75 --recode`

After filtering, kept 179 out of 179 Individuals

After filtering, kept 13232 out of a possible 56218 Sites

Final vcf: maxdp30_mindp2_md75.vcf

***
