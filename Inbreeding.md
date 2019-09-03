## Inbreeding estimates


### Inbreeding coefficient: Fcoff

`vcftools --vcf filtered.vcf --het`

##### OR

`plink --vcf filtered.vcf --allow-extra-chr --double-id --out filtered_test`

`plink --bfile filtered_test --recode --tab --allow-extra-chr --out filtered_test`

`plink --bfile filtered_test --allow-extra-chr --freq --missing --het --ibc --out filtered_test`


##### VCFtools --het and PLINK --het provide same output


***




### InbreedR: sMLH

### In R-Studio

`library(inbreedR)`

##### calculate sMLH

`het <- sMLH(kakapo_snp_genotypes)`

`het`

`write.table(het, "sMLH_kakapo.txt", sep="\t")`

`plot(het)`

`plot(het, main = "sMLH",
     col = "cornflowerblue", cex.axis=0.95)`


##### variance in sMLH
`het_var <- var(het)`

`het_var`

`plot(het_var, main = "Variance in sMLH", col = "cornflowerblue", cex.axis=0.95)`


##### calculate g2 ID 
`g2_kakapo_snps <- g2_snps(kakapo_snp_genotypes, nperm = 100, nboot = 10, CI = 0.95, parallel = FALSE, ncores = NULL)`

`g2_kakapo_snps`

`plot(g2_kakapo_snps, main = "Identity disequilibrium", col = "cornflowerblue", cex.axis=0.95)`

###### plot shows the distribution of bootstrap results including the confidence interval.


##### calcualte het-het corr 
`HHC_kakapo_snps <- HHC(kakapo_snp_genotypes, reps = 100)`

`HHC_kakapo_snps`

`plot(HHC_kakapo_snps, main = "Heterozygosity-heterozygosity correlations", col = "cornflowerblue", cex.axis=0.85)`


##### r^2 between inbreeding and heterozygosity
`hf <- r2_hf(genotypes = kakapo_snp_genotypes, nboot = 100, type = "snps", parallel = FALSE)`

`hf`

`plot(hf)`

`plot(hf, main = "r2 bootstrapping distribution - estimated r2 with CI", col = "cornflowerblue", cex.axis=0.85)`


#wrecked laptop rip
#sim_g2 <- simulate_g2(n_ind = 180, H_nonInb = 0.5, meanF = 0.2, varF = 0.03, subsets = seq(from = 1000, to = 14000, by = 1000), reps = 100, type = "snps")
`

***


#### KGD Fgrm

### In R-Studio

`genofile <- "maxdp.vcf.ra.tab"`

`gform <- "Tassel"`

`source("GBS-Chip-Gmatrix.R")`

`Gfull      <- calcG()`

`GHWdgm.05  <- calcG(which(HWdis > -0.05), "HWdgm.05", npc = 4)`

###### recalculate using Hardy-Weinberg disequilibrium cut-off at -0.05

`write.table(Gfull$G5,"G5.txt",sep="\t")`


***


#### Runs of Homozygosity in PLINK

`plink --vcf filtered.vcf --allow-extra-chr --double-id --out filtered_test`

`plink --bfile filtered_test --recode --tab --allow-extra-chr --out filtered_test`

`plink --file filtered_test --allow-extra-chr --homozyg --homozyg-snp 25 --homozyg-het 1 --homozyg-density 180 --homozyg-gap 1000 --homozyg-window-snp 25 --homozyg-window-het 1 --homozyg-window-missing 5 --homozyg-window-threshold 0.05 --out filtered_roh`


***
