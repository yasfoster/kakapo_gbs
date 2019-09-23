## VCFtools filtering

### Remove sex chromosomes so downstream analyses performed on neutral autosomal SNPs

Z chromosome CM013763.1

W chromosome CM013773.1


#### Initial filtering: remove sex chomosomes and fitltered for minimum depth 2 & max depth 30, for unfiltered.vcf i.e. straight outta STACKS

`vcftools --vcf 1_unfilt.vcf --not-chr CM013763.1 --not-chr CM013773.1 --minDP 2 --maxDP 30 --recode`

After filtering, kept 182 out of 182 Individuals

After filtering, kept 56218 out of a possible 58466 Sites

***

##### After, --missing-indv to generate file reporting the missingness on a per-individual basis

`vcftools --vcf unfilt.vcf --missing-indv`

##### removed individuals with high missingness >70%

`vcftools --vcf filt_sexchr_minmaxdp.vcf --remove-indv Sarah --remove-indv Tiaho --remove-indv Weheruatanga --remove-indv Waikawa --remove-indv Palmersan --remove-indv Ruth_16_1_1_YM --remove-indv Maestro --remove-indv Waa --remove-indv Sirocco --remove-indv Pea_16_1_1_B --remove-indv Evohe --remove-indv Moorhouse --remove-indv Sue_16_1_1_B --remove-indv Hananui --remove-indv Roha --remove-indv Awhero --recode`

After filtering, kept 166 out of 182 Individuals

After filtering, kept 56218 out of a possible 56218 Sites

#### VCF for KGD > Fgrm
##### minimal filtering as KGD applies its own (e.g. sample & SNP depth > 0.01)
###### Final vcf: KGD_maxdp30_mindp2.vcf

***

#### VCF for Fcoff, sMLH & RoH 
##### most stringent filtering
##### filtered for depth, and allowing for up to 20% missing data

`vcftools --vcf maxdp30_mindp2.vcf --max-missing 0.80 --recode`

After filtering, kept 12241 out of a possible 56218 Site

###### Final vcf: filtered_md80.vcf

***
