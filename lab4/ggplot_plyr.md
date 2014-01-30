


_Lattice_ vs _Ggplot2_ and _aggregate_ vs _plyr_
==========================================

Goal: to get a little more familiar with the awesome _ggplot2_ and _plyr_ functions by mimicking some results that were obtained with the older _lattice_ and _aggregate_

[Link to original tutorial](http://www.ugrad.stat.ubc.ca/~stat540/seminars/seminar04_compileNotebook-dataAggregation-twoGroupComparison.html)

#### Data preparation
Load the required libraries and data

```r
library(lattice)
library(plyr)
library(ggplot2)
prDat <- read.table("GSE4051_data.tsv")
prDes <- readRDS("GSE4051_design.rds")
```


#### Single gene exploration

Extract data for one gene

```r
set.seed(987)
theGene <- sample(1:nrow(prDat), 1)
pDat <- data.frame(prDes, gExp = unlist(prDat[theGene, ]))
```


Explore!  
What are sample means in wildtype/knockout? First using _aggregate_, then using _plyr_
```
aggregate(gExp ~ gType, pDat, FUN = mean)
```
<!-- html table generated in R 3.0.1 by xtable 1.7-1 package -->
<!-- Wed Jan 29 23:10:46 2014 -->
<TABLE border=1>
<TR> <TH> gType </TH> <TH> gExp </TH>  </TR>
  <TR> <TD> wt </TD> <TD align="right"> 9.76 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD align="right"> 9.55 </TD> </TR>
   </TABLE>

```
ddply(pDat, ~ gType, summarize, gExp = mean(gExp))
```
<!-- html table generated in R 3.0.1 by xtable 1.7-1 package -->
<!-- Wed Jan 29 23:10:46 2014 -->
<TABLE border=1>
<TR> <TH> gType </TH> <TH> gExp </TH>  </TR>
  <TR> <TD> wt </TD> <TD align="right"> 9.76 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD align="right"> 9.55 </TD> </TR>
   </TABLE>


Make sure the two actually returned identical results

```r
identical(aggregate(gExp ~ gType, pDat, FUN = mean),
          ddply(pDat, ~ gType, summarize, gExp = mean(gExp)))
```

```
## [1] TRUE
```


Plot!  
Strip plot of just the one gene, knockout vs wildtype. First using _lattice_, then using _ggplot2_

```r
stripplot(gType ~ gExp, pDat)
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-71.png) 

```r
ggplot(pDat, aes(x = gExp, y = gType)) + geom_point()
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-72.png) 


#### Multiple genes

Load in the dataset

```r
kDat <- readRDS("GSE4051_MINI.rds")
```


Explore!  
Average expression of eggBomb over developmental stages, first using _aggregate_, then using _plyr_
```
aggregate(eggBomb ~ devStage, kDat, FUN = mean)
```
<!-- html table generated in R 3.0.1 by xtable 1.7-1 package -->
<!-- Wed Jan 29 23:10:47 2014 -->
<TABLE border=1>
<TR> <TH> devStage </TH> <TH> eggBomb </TH>  </TR>
  <TR> <TD> E16 </TD> <TD align="right"> 6.88 </TD> </TR>
  <TR> <TD> P2 </TD> <TD align="right"> 6.41 </TD> </TR>
  <TR> <TD> P6 </TD> <TD align="right"> 6.46 </TD> </TR>
  <TR> <TD> P10 </TD> <TD align="right"> 7.14 </TD> </TR>
  <TR> <TD> 4_weeks </TD> <TD align="right"> 7.06 </TD> </TR>
   </TABLE>

```
ddply(kDat, ~ devStage, summarize, exp = mean(eggBomb))
```
<!-- html table generated in R 3.0.1 by xtable 1.7-1 package -->
<!-- Wed Jan 29 23:10:47 2014 -->
<TABLE border=1>
<TR> <TH> devStage </TH> <TH> exp </TH>  </TR>
  <TR> <TD> E16 </TD> <TD align="right"> 6.88 </TD> </TR>
  <TR> <TD> P2 </TD> <TD align="right"> 6.41 </TD> </TR>
  <TR> <TD> P6 </TD> <TD align="right"> 6.46 </TD> </TR>
  <TR> <TD> P10 </TD> <TD align="right"> 7.14 </TD> </TR>
  <TR> <TD> 4_weeks </TD> <TD align="right"> 7.06 </TD> </TR>
   </TABLE>


Same thing, but now aggregate based on dev stage AND genotype
```
aggregate(eggBomb ~ gType * devStage, kDat, FUN = mean)
```
<!-- html table generated in R 3.0.1 by xtable 1.7-1 package -->
<!-- Wed Jan 29 23:10:47 2014 -->
<TABLE border=1>
<TR> <TH> gType </TH> <TH> devStage </TH> <TH> eggBomb </TH>  </TR>
  <TR> <TD> wt </TD> <TD> E16 </TD> <TD align="right"> 6.90 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> E16 </TD> <TD align="right"> 6.85 </TD> </TR>
  <TR> <TD> wt </TD> <TD> P2 </TD> <TD align="right"> 6.61 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> P2 </TD> <TD align="right"> 6.21 </TD> </TR>
  <TR> <TD> wt </TD> <TD> P6 </TD> <TD align="right"> 6.65 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> P6 </TD> <TD align="right"> 6.27 </TD> </TR>
  <TR> <TD> wt </TD> <TD> P10 </TD> <TD align="right"> 7.04 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> P10 </TD> <TD align="right"> 7.24 </TD> </TR>
  <TR> <TD> wt </TD> <TD> 4_weeks </TD> <TD align="right"> 7.12 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> 4_weeks </TD> <TD align="right"> 7.01 </TD> </TR>
   </TABLE>

```
ddply(kDat, .(gType, devStage), summarize, exp = mean(eggBomb))
```
<!-- html table generated in R 3.0.1 by xtable 1.7-1 package -->
<!-- Wed Jan 29 23:10:47 2014 -->
<TABLE border=1>
<TR> <TH> gType </TH> <TH> devStage </TH> <TH> exp </TH>  </TR>
  <TR> <TD> wt </TD> <TD> E16 </TD> <TD align="right"> 6.90 </TD> </TR>
  <TR> <TD> wt </TD> <TD> P2 </TD> <TD align="right"> 6.61 </TD> </TR>
  <TR> <TD> wt </TD> <TD> P6 </TD> <TD align="right"> 6.65 </TD> </TR>
  <TR> <TD> wt </TD> <TD> P10 </TD> <TD align="right"> 7.04 </TD> </TR>
  <TR> <TD> wt </TD> <TD> 4_weeks </TD> <TD align="right"> 7.12 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> E16 </TD> <TD align="right"> 6.85 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> P2 </TD> <TD align="right"> 6.21 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> P6 </TD> <TD align="right"> 6.27 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> P10 </TD> <TD align="right"> 7.24 </TD> </TR>
  <TR> <TD> NrlKO </TD> <TD> 4_weeks </TD> <TD align="right"> 7.01 </TD> </TR>
   </TABLE>


#### Multigene plotting
Grab 6 genes: 3 interesting, 3 boring

```r
keepGenes <- c("1431708_a_at", "1424336_at", "1454696_at",
               "1416119_at", "1432141_x_at", "1429226_at")
miniDat <- subset(prDat, rownames(prDat) %in% keepGenes)
miniDat <- data.frame(gExp = as.vector(t(as.matrix(miniDat))),
                      gene = factor(rep(rownames(miniDat), each = ncol(miniDat)),
                                    levels = keepGenes))
miniDat <- suppressWarnings(data.frame(prDes, miniDat))
```


Plot!  
Strip plot of the expression vs genotype, one plot per gene. First using _lattice_, then using _ggplot2_

```r
stripplot(gType ~ gExp | gene, miniDat,
          scales = list(x = list(relation = "free")),
          group = gType, auto.key = TRUE)
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-141.png) 

```r
ggplot(miniDat, aes(x = gExp, y = gType, color = gType)) +
  facet_wrap(~ gene, scales="free_x") +
  geom_point(alpha = 0.7) +
  theme(panel.grid.major.x = element_blank())
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-142.png) 

