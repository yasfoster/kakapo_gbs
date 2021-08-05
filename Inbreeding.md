## Inbreeding estimates for kākāpō

See methods section for summary of each inbreeding metric, including other aliases, mathematical equations etc. 

### Inbreeding coefficient: FH

##### Vcftools v.1.14 --het calculates a measure of heterozygosity on a per-individual basis. Specfically, the inbreeding coefficient (FH) is estimated for each individual using method of moments

For more details, see:

https://vcftools.github.io/man_latest.html

https://www.cog-genomics.org/plink/1.9/basic_stats#ibc

`vcftools --vcf filtered.vcf --het`

##### OR, PLINK v1.9 --het

```
plink --vcf filtered.vcf --allow-extra-chr --double-id --out filtered_test

plink --bfile filtered_test --recode --tab --allow-extra-chr --out filtered_test

plink --bfile filtered_test --allow-extra-chr --het --ibc --out filtered_test
```

##### VCFtools --het and PLINK --het provide same output

###### PLINK --het computes observed and expected autosomal homozygous genotype counts for each sample, and reports method-of-moments F coefficient estimates 
###### PLINK --ibc (ported from GCTA) calculates three inbreeding coefficients for each sample. Briefly, Fhat1 is the usual variance-standardized relationship minus 1, Fhat2 is approximately equal to the --het estimate, and Fhat3 is based on the correlation between uniting gametes.


***
***



### InbreedR: standardised multilocus heterozygosity (sMLH), identity disequilibrium (g2), and heterozygosity-heterozygosity correlation coefficients

##### "Using SNP markers, it is possible to measure variance in inbreeding through the strength of correlation in heterozygosity across marker loci, termed identity disequilibrium (ID). ID can be quantified using the measure g2, which is also a central parameter in HFC theory that can be used within a wider framework to estimate the direct impact of inbreeding on both marker heterozygosity and fitness."(Stoffel et al. 2016).

For more details, see:

https://besjournals.onlinelibrary.wiley.com/doi/10.1111/2041-210X.12588

https://cran.r-project.org/web/packages/inbreedR/vignettes/inbreedR_step_by_step.html

#### In R-Studio
##### convert VCF using vcfR & reshape2

```
library(vcfR)

library(reshape2)

vcf_file <- "filtered.vcf
```

##### read vcf

`vcf <- read.vcfR(vcf_file, verbose = FALSE )`

##### extract genotypes

`gt <- extract.gt(vcf)`

##### transpose and data.frame

`gt <- as.data.frame(t(gt), stringsAsFactors = FALSE)`

##### NA handling

`gt[gt == "."] <- NA`

##### split columns

`snp_geno <- do.call(cbind, apply(gt, 2, function(x) colsplit(x, "/", c("a","b"))))`

##### convert

`kakapo_snp_genotypes <- inbreedR::convert_raw(snp_geno)`

***

##### check data
```
library(inbreedR)

check_data(kakapo_snp_genotypes)
```
***

#### calculate sMLH
```
library(inbreedR)

het <- sMLH(kakapo_snp_genotypes)

het

write.table(het, "sMLH_kakapo.txt", sep="\t")

plot(het)

plot(het, main = "sMLH",
     col = "cornflowerblue", cex.axis=0.95)
```

##### variance in sMLH
```
het_var <- var(het)

het_var

plot(het_var, main = "Variance in sMLH", col = "cornflowerblue", cex.axis=0.95)
```

##### calculate g2 ID 
```
g2_kakapo_snps <- g2_snps(kakapo_snp_genotypes, nperm = 100, nboot = 10, CI = 0.95, parallel = FALSE, ncores = NULL)

g2_kakapo_snps

plot(g2_kakapo_snps, main = "Identity disequilibrium", col = "cornflowerblue", cex.axis=0.95)
```
###### plot shows the distribution of bootstrap results including the confidence interval


##### calcualte het-het corr 
```
HHC_kakapo_snps <- HHC(kakapo_snp_genotypes, reps = 100)

HHC_kakapo_snps

plot(HHC_kakapo_snps, main = "Heterozygosity-heterozygosity correlations", col = "cornflowerblue", cex.axis=0.85)
```

##### r^2 between inbreeding and heterozygosity
```
hf <- r2_hf(genotypes = kakapo_snp_genotypes, nboot = 100, type = "snps", parallel = FALSE)

hf

plot(hf)

plot(hf, main = "r2 bootstrapping distribution - estimated r2 with CI", col = "cornflowerblue", cex.axis=0.85)
```

***
***



## Kinship using GBS with depth adjustment (KGD): Fgrm

##### "Unbiased estimates of relatedness can be obtained by using only those SNPs with genotype calls in both individuals. The expected value of this estimator is independent of the SNP depth in each individual, under a model of genotype calling that includes the special case of the two alleles being read at random" (Dodds et al. 2015)

##### In contrast, the estimator of self-relatedness (diagonal of GRM = Fgrm) does depend on the SNP depth, providing unbiased estimates of self-relatedness

For more details, see:

https://github.com/AgResearch/KGD

https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-015-2252-3

##### Convert VCF for KGD

```python
python2.7 vcf2ra.py filtered.vcf
```

#### In R-Studio
```
genofile <- "samples.vcf.ra.tab"

gform <- "Tassel"

source("GBS-Chip-Gmatrix.R")

Gfull      <- calcG()

write.table(Gfull$G5,"G5.txt",sep="\t")

write.table(Gfull_heatmap$G5, "G5_heatmap.txt", sep="\t")
```

###### In Excel, extract diagonal of G5 matrix for self-relatedness estimates, Fgrm = diagonal elements of matrix minus one

`=INDEX(A1:E1,,ROWS($1:1))`

`Fgrm = diagonal of matrix - 1` 


***
***



## Runs of homozygosity (RoH) in PLINK: Froh

##### Following parameters similar to Grossen et al. 2018 and Kardos, Luikart & Allendorf et al. 2015

For more details, see:

https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5775499/pdf/EVA-11-123.pdf

https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4815495/pdf/hdy201517a.pdf

##### Difference between Grossen et al. & Kardos papers is parameter --homozyg-density (kb/snp)

###### Average kakapo SNP density for 12,241 SNPs & autosomal genome lenth of 1.028666775 Gb:
###### 1028666.775/12241 = 84.03453762 kb/SNPs, then PLINK parameter density (according to Kardos et al.) is real SNP density x 1.5 
###### SNP density = 126.0518064 kb/SNPs, so --homozy-density 130 is most suited to the data
```
plink --vcf filtered.vcf --allow-extra-chr --double-id --out filtered_test

plink --bfile filtered_test --recode --tab --allow-extra-chr --out filtered_test

plink --file filtered_roh_80 --allow-extra-chr --homozyg --homozyg-snp 25 --homozyg-het 1 --homozyg-density 130 --homozyg-gap 1000 --homozyg-window-snp 25 --homozyg-window-het 1 --homozyg-window-missing 5 --homozyg-window-threshold 0.05 --out roh_130d
```
#SNP Density 130 kb/SNP, >25 SNPs per ROH
#--homozyg: Scan complete, found 9372 ROH

####ADDITIONAL PARAMETER TESTING: in addition to calculating SNP density, the total length of RoH (Mb, from .hom.indv output) plotted against frequency of missing data per individual (vcftools --missing-indv), to see effect of missing data on calling of RoH

####130 DENSITY IS MOST SUITABLE: doesn't inflate individuals with larger missing data i.e. individuals with missing data aren't calling more/less RoH than those with more markers

####Allowing up to three heterozygous sites (--homozyg-het 3) as suggested in Ceballos et al.(2018), did not impact the number of RoH found.


############################






***
