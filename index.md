
``` r
library(netplot)
```

    Loading required package: grid

``` r
library(igraph)
```


    Attaching package: 'igraph'

    The following object is masked from 'package:netplot':

        ego

    The following objects are masked from 'package:stats':

        decompose, spectrum

    The following object is masked from 'package:base':

        union

``` r
library(data.table)
library(rgexf)

edgelist <- fread("edgelist.csv")
vertices <- fread("vertices.csv")

# Checking which edgelist vertex is not in the list of vertices
vnames_in_edgelist <- edgelist[,c(source, target)]
vnames_in_edgelist[
  which(!vnames_in_edgelist %in% vertices[, name])
]
```

    character(0)

``` r
net <- graph_from_data_frame(edgelist, directed = FALSE, vertices = vertices) 
```

``` r
set.seed(331)
np <- nplot(
  net,
  vertex.color = ~type,
  vertex.size.range = c(.015, .02),
  edge.color = ~ego + alter,
  edge.curvature = pi/6,
  vertex.label.show = 1
  )

V(net)$color <- get_vertex_gpar(np, "core", "col")$col
E(net)$color <- get_edge_gpar(np, "line", "col")$col[1,]
np
```

![](index_files/figure-commonmark/unnamed-chunk-2-1.svg)

``` r
# Saving the gexf object
pos <- cbind(np$.layout, 0)
colnames(pos) <- c("x", "y", "z")
net_gexf <- igraph.to.gexf(net, nodesVizAtt = list(position = pos))
plot(net_gexf, dir = "docs", nodeSizeFactor = .5)
```
