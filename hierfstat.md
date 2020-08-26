###NEED TO DEFINE BETA FUNCTION IN R: hierfstat package up-to-date on R, but function was not updated so need to run manually. See J Goudet github pull requests.

```function (dat, nboot = 0, lim = c(0.025, 0.975), diploid = TRUE) 
{
    ratio.Hi.Hb <- function(x) {
        dum <- which(!is.na(x))
        sum(x[dum])/sum(Hb[dum])
    }
    if (is.genind(dat)) 
        dat <- genind2hierfstat(dat)
    pfr <- pop.freq(dat, diploid)
    pfr2 <- lapply(pfr, function(x) t(x) %*% x)
    nl <- dim(dat)[2] - 1
    np <- length(table(dat[, 1]))
    ns <- ind.count.n(dat)
    if (diploid) 
        ns <- ns * 2
    ns[is.na(ns)] <- 0
    Hi <- matrix(numeric(np * nl), ncol = nl)
    Hb <- numeric(nl)
    for (il in 1:nl) {
        Hi[, il] <- ns[, il]/(ns[, il] - 1) * (1 - diag(pfr2[[il]]))
        npl <- sum(ns[, il] > 0, na.rm = TRUE)
        Hb[il] <- 1 - 1/npl/(npl - 1) * sum((pfr2[[il]] - diag(diag(pfr2[[il]]))), 
            na.rm = TRUE)
    }
    betai <- 1 - apply(Hi, 1, ratio.Hi.Hb)
    if (nboot < 100) {
        return(list(betaiovl = betai, Hi = Hi, Hb = Hb))
    }
    else {
        if (nl < 10) {
            warning("Less than 10 loci, can't estimate Conf. Int.")
            return(list(betaiovl = betai, Hi = Hi, Hb = Hb))
        }
        boot.bi <- matrix(numeric(nboot * np), nrow = nboot)
        nls <- apply(ns, 1, function(x) which(x > 0))
        if (is.matrix(nls)) {
            for (ib in 1:nboot) {
                for (ip in 1:np) {
                  dum <- sample(nls[, ip], replace = TRUE)
                  boot.bi[ib, ip] <- 1 - sum(Hi[ip, dum])/sum(Hb[dum])
                }
            }
        }
        else {
            for (ib in 1:nboot) {
                for (ip in 1:np) {
                  dum <- sample(nls[[ip]], replace = TRUE)
                  boot.bi[ib, ip] <- 1 - sum(Hi[ip, dum])/sum(Hb[dum])
                }
            }
        }
        bi.ci <- apply(boot.bi, 2, quantile, lim, na.rm = TRUE)
        return(list(betaiovl = betai, ci = bi.ci, Hi = Hi, Hb = Hb))
    }
}
<bytecode: 0x7f95040da4b8>
<environment: namespace:hierfstat>
```
