Heatmaps in ggplot2
========================================================

Goal: to produce a similar heatplot to the one in the lattice tutorial using ggplot2.

[Lattice tutorial](http://www.ugrad.stat.ubc.ca/~stat540/seminars/seminar03_graphics-lattice.html#heatmaps)  
[ggplot2 tutorial](http://www.ugrad.stat.ubc.ca/~stat540/seminars/seminar03_graphics-ggplot2.html)

#### Data preparation
Load the required libraries and data

```r
library(ggplot2)
library(RColorBrewer)
prDat <- read.table("GSE4051_data.tsv")
prDes <- readRDS("GSE4051_design.rds")
```


Set seed so that the "random" sample we select is the same as the one from the tutorial

```r
set.seed(1)
```


Choose 50 probes (out of the 30k)

```r
yo <- sample(1:nrow(prDat), size = 50)
hDat <- prDat[yo, ]
colnames(hDat) <- with(prDes, paste(devStage, gType, sidChar, sep = "_"))
```


#### ggplot2
Transform the data into long format that will be easy to plot
Note that the lattice heatmap expects a matrix, but (as far as I can tell), ggplot heatmap expects a dataframe, so we need to convert the 2D matrix of values into a list of tuples in the form of <sample>, <probe>, <value>


```r
prDatTall <- data.frame(sample = rep(colnames(hDat), each = nrow(hDat)),
                        probe = rownames(hDat),
                        expression = unlist(hDat))
```


Create a blue -> purple colour palette

```r
jBuPuFun <- colorRampPalette(brewer.pal(n = 9, "BuPu"))
paletteSize <- 256
jBuPuPalette <- jBuPuFun(paletteSize)
```


Plot the heatmap.  
Note the [geom_tile](http://docs.ggplot2.org/current/geom_tile.html) is the graphic element that is used for heatmaps.

```r
ggplot(prDatTall, aes(x = probe, y = sample, fill = expression)) +
  theme(axis.text.x = element_text(angle = 90, hjust = 1, vjust = 0.5)) +
  geom_tile() +
  scale_fill_gradient2(low = jBuPuPalette[1],
                       mid = jBuPuPalette[paletteSize/2],
                       high = jBuPuPalette[paletteSize],
                       midpoint = (max(prDatTall$expression) + min(prDatTall$expression)) / 2,
                       name = "Expression")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 

Look at the _scale_fill_gradient2_ documentation for an explanation of the parameters that were used


#### Lattice
In lattice, the _heatmap_ function expects a matrix, so we create a matrix of the data and then plot it

```r
hDat <- as.matrix(t(hDat))
heatmap(hDat, Rowv = NA, Colv = NA, scale = "none", col = jBuPuPalette)
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7.png) 



If you track the pixel of any sample/probe pair and compare it in both heatmaps, it should be similar.  Note that the heatmaps themselves are not exactly congruent because the order of the samples in our ggplot version is not chronological, whereas in the lattice version they are.  This is left as an exercise :)
