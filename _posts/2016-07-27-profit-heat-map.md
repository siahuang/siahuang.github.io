---
layout: default
title: "Profit Heat Map using R"
author: "Sia Huang"
date: "Oct 18, 2015"
output:
  html_document:
    keep_md: yes
    theme: united
    toc: yes
---
### What the Data Looks Like

The dataset contains weekly sales records of a coffee chain store.
It has the following columns:
```{r echo=FALSE}
cc <- read.csv("Coffee Chain Data.csv")
colnames(cc)
```

### Mission

Create a profit heat map that shows profit/loss and size (sum of absolute profit) broken down by Product vs. Market.

### Coding & Result
Step 1.

Perform calculation of total profit by product and market first.
Use the aggregate function.
```{r}
prof.prod <- aggregate(Profit~Product+Market, cc, sum)
```

Step 2.

Determine profit or loss.
```{r}
porl <- ifelse(prof.prod$Profit>0, "profit","loss")
```

Step 3.

Scale down "profit or loss" to control the size of graph marks.

Since 27000 is near the highest profit, it can serve as baseline. 
```{r}
scale <- sqrt(abs((prof.prod$Profit)/27000))
```

Step 4.

Make and design a graph using ggplot2
```{r message=FALSE}
library(ggplot2)
ggplot(prof.prod, aes(Product, Market, fill=porl))+     #define x- and y-axis and marks
    geom_tile(width=scale, height=scale)+               #design marks' size
    scale_fill_manual(values = c("red","darkgreen"))+     #design marks' fill colors
    theme(axis.text.x=element_text(angle=90, hjust=1, size=12))+     #design x-axis' font and format
    scale_y_discrete(limits=rev(levels(prof.prod$Market)))+     #change y-axis display to alphabetical order
    theme(panel.grid.major = element_blank(), panel.background =element_blank())+     
    #change plot theme by eliminating grid and background color
    ggtitle("Profit Heat Map")+     #define plot title
    coord_fixed()     #fix coordinates' ratio so that the plot won't change disproportionally regardless of how the display window changes
```

### What we can tell from the graph
Best-selling product type by market:

* Central - Caffe Mocha
* East - Colombian
* South - Colombian
* West - Lemon
Among these, the sales of Colombian coffee in East market represented the biggest profitability.

Although Caffe Mocha was popular in Central market but it sold at loss in East market. The chain store may reallocate some of its Caffe mocha inventory to Central market based on demand forecasting.

In West market, Amaretto, Decaf Irish Cream and Green tea were all sold at loss. The coffee chain may need to take a look at the market research result in this market, and make sure that it sells right product to the right people.
