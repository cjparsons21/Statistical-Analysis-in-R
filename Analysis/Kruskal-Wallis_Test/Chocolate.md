---
title: "My Analysis"
output: 
  html_document:
    theme: cerulean
    code_folding: hide
    keep_md: TRUE
---


```r
# Be sure to use your file menus at the top of R-Studio to:
# "Session -> Set working directory -> To source file location"
# Then play this chunk and the next one to get the data into R.
library(mosaic)
library(car)
library(DT)
library(pander)
library(readr)
library(plotly)
library(tidyverse)
choc <- read_csv("~/Documents/M325_Git/Data/Chocolate.csv")


mylocs <- table(choc$Location) 
mylocnames <- names(mylocs[mylocs>90])
choc.loc <- subset(choc, Location %in% mylocnames)
```





<!-- Instructions:

 Use the Chocolate dataset and a Kruskal-Wallis Test(s) to answer an interesting question(s) that you come up with.
 
 About the dataset:
 
  * Company: Name of the company manufacturing the bar.

  * Orig: The specific geo-region of origin for the bar.

  * Review: Date of publication of the review.

  * Cocoa: Cocoa percentage (darkness) of the chocolate bar being reviewed.

  * Location: Manufacturer base country.

  * Rating: Expert rating for the bar.
      5= Elite (Transcending beyond the ordinary limits)
      4= Premium (Superior flavor development, character and style)
      3= Satisfactory(3.0) to praiseworthy(3.75) (well made with special qualities)
      2= Disappointing (Passable but contains at least one significant flaw)
      1= Unpleasant (mostly unpalatable)

   * Type: The variety (breed) of cocoa bean used, if provided.
   
   * BroadOrig: The broad geo-region of origin for the bean.

Note this dataset and description come from: © 2018 Kaggle Inc for further details visit:

[Kaggle.com](https://www.kaggle.com/rtatman/chocolate-bar-ratings/data)



<!--Begin writing your Analysis after this point.-->
## Chocolate Ratings {.tabset .tabset-fade}


### Background
<div>

This analysis used the Chocolate dataset from Kaggle. The dataset included (among other things) different types of chocolate from 60 different countries rated on a scale of 1 to 5. I chose to narrow my search down to the four countries with the most samples of chololate given in the study. This lead to only using data from Canada, France, the U.K., and the United States.

$$
  H_0:  \text{All samples are from the same distribution}
$$
$$
  H_a:  \text{At least one sample's distribution is stochastically different}
$$


 I used a Kruskal-Wallis Test with a level of significance of .05 to evaluate if at least one of these four countries came from a different population than the rest based on their chocolate quality rating. The rating scale used is shown below.

Rating: Expert rating of the bar:
 
  5= Elite (Transcending beyond the ordinary limits)
  
  4= Premium (Superior flavor development, character and style)
  
  3= Satisfactory(3.0) to praiseworthy(3.75) (well made with special qualities)
  
  2= Disappointing (Passable but contains at least one significant flaw)
  
  1= Unpleasant (mostly unpalatable)

*Note that only two samples from the entire dataset were rated above 4 so we will consider the Premium rating a noteworthy accomplishment instead of being just second-best.*
  
   
</div>

### Analysis
<div>

Preforming a Kruskal-Wallis test veryfied that at least one of the four countries comes from a different population than the others. However, this is rather uninformative as we still are missing a large amount of information. We don't know which country is different than that others, if there is more than one country in this category, or if that country has better or worse chocolate than the others. All we know is that the data is worth a deeper investigation.


```r
pander(kruskal.test(data=choc.loc, as.factor(Rating)~as.factor(Location)))
```


--------------------------------------
 Test statistic   df      P value     
---------------- ---- ----------------
     26.63        3    7.05e-06 * * * 
--------------------------------------

Table: Kruskal-Wallis rank sum test: `as.factor(Rating)` by `as.factor(Location)`

Using a boxplot to give insights into the aforementioned shortcomings of the information provided by the Kruskal-Wallis test led to some interesting revelations. 


```r
plot_ly(data=choc.loc, x=~Location, y=~Rating, type="box") %>%
  layout(
    title = "Chocolate Bar Ratings",
    xaxis = list(title = "Country"),
    yaxis = list(title = "Chocolate Bar Rating",
                 range=c(0,4), 
                 tickvals=seq(1,4,1), 
                 tickmode="array"))
```

<!--html_preserve--><div id="6542644f7d23" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="6542644f7d23">{"x":{"visdat":{"6542216ab400":["function () ","plotlyVisDat"]},"cur_data":"6542216ab400","attrs":{"6542216ab400":{"x":{},"y":{},"alpha":1,"sizes":[10,100],"type":"box"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"Chocolate Bar Ratings","xaxis":{"domain":[0,1],"title":"Country","type":"category","categoryorder":"array","categoryarray":["Canada","France","U.K.","U.S.A."]},"yaxis":{"domain":[0,1],"title":"Chocolate Bar Rating","range":[0,4],"tickvals":[1,2,3,4],"tickmode":"array"},"hovermode":"closest","showlegend":false},"source":"A","config":{"modeBarButtonsToAdd":[{"name":"Collaborate","icon":{"width":1000,"ascent":500,"descent":-50,"path":"M487 375c7-10 9-23 5-36l-79-259c-3-12-11-23-22-31-11-8-22-12-35-12l-263 0c-15 0-29 5-43 15-13 10-23 23-28 37-5 13-5 25-1 37 0 0 0 3 1 7 1 5 1 8 1 11 0 2 0 4-1 6 0 3-1 5-1 6 1 2 2 4 3 6 1 2 2 4 4 6 2 3 4 5 5 7 5 7 9 16 13 26 4 10 7 19 9 26 0 2 0 5 0 9-1 4-1 6 0 8 0 2 2 5 4 8 3 3 5 5 5 7 4 6 8 15 12 26 4 11 7 19 7 26 1 1 0 4 0 9-1 4-1 7 0 8 1 2 3 5 6 8 4 4 6 6 6 7 4 5 8 13 13 24 4 11 7 20 7 28 1 1 0 4 0 7-1 3-1 6-1 7 0 2 1 4 3 6 1 1 3 4 5 6 2 3 3 5 5 6 1 2 3 5 4 9 2 3 3 7 5 10 1 3 2 6 4 10 2 4 4 7 6 9 2 3 4 5 7 7 3 2 7 3 11 3 3 0 8 0 13-1l0-1c7 2 12 2 14 2l218 0c14 0 25-5 32-16 8-10 10-23 6-37l-79-259c-7-22-13-37-20-43-7-7-19-10-37-10l-248 0c-5 0-9-2-11-5-2-3-2-7 0-12 4-13 18-20 41-20l264 0c5 0 10 2 16 5 5 3 8 6 10 11l85 282c2 5 2 10 2 17 7-3 13-7 17-13z m-304 0c-1-3-1-5 0-7 1-1 3-2 6-2l174 0c2 0 4 1 7 2 2 2 4 4 5 7l6 18c0 3 0 5-1 7-1 1-3 2-6 2l-173 0c-3 0-5-1-8-2-2-2-4-4-4-7z m-24-73c-1-3-1-5 0-7 2-2 3-2 6-2l174 0c2 0 5 0 7 2 3 2 4 4 5 7l6 18c1 2 0 5-1 6-1 2-3 3-5 3l-174 0c-3 0-5-1-7-3-3-1-4-4-5-6z"},"click":"function(gd) { \n        // is this being viewed in RStudio?\n        if (location.search == '?viewer_pane=1') {\n          alert('To learn about plotly for collaboration, visit:\\n https://cpsievert.github.io/plotly_book/plot-ly-for-collaboration.html');\n        } else {\n          window.open('https://cpsievert.github.io/plotly_book/plot-ly-for-collaboration.html', '_blank');\n        }\n      }"}],"cloud":false},"data":[{"x":["France","France","France","France","France","France","France","France","France","France","France","France","France","France","France","France","France","France","U.S.A.","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","France","France","France","France","France","France","France","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","U.S.A.","U.S.A.","France","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.S.A.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","France","France","France","France","France","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","Canada","Canada","U.S.A.","Canada","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.K.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","France","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","U.S.A.","U.S.A.","U.S.A.","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","France","U.S.A.","U.S.A.","U.K.","U.S.A.","France","France","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","U.K.","U.K.","U.S.A.","U.S.A.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","U.K.","U.S.A.","France","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","France","U.S.A.","U.S.A.","U.S.A.","France","U.S.A.","France","France","France","France","U.S.A.","France","U.S.A.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","Canada","Canada","U.S.A.","U.S.A.","France","U.S.A.","U.S.A.","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.S.A.","U.S.A.","U.S.A.","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","Canada","U.S.A.","U.S.A.","France","France","U.S.A.","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","U.S.A.","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","France","U.S.A.","U.S.A.","Canada","U.S.A.","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.S.A.","U.K.","Canada","Canada","U.S.A.","U.S.A.","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","U.S.A.","U.S.A.","France","France","France","France","France","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","France","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.S.A.","U.S.A.","France","France","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","France","U.S.A.","U.S.A.","Canada","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","U.S.A.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","U.K.","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","France","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","France","France","France","France","France","France","France","France","France","U.K.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","Canada","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","U.S.A.","U.S.A.","France","France","France","France","U.S.A.","France","U.S.A.","U.S.A.","U.K.","U.K.","U.K.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","U.S.A.","France","U.S.A.","U.K.","Canada","U.S.A.","U.S.A.","France","Canada"],"y":[3.75,2.75,3,3.5,3.5,3.5,4,2.75,3.25,3.75,2.75,3,3.25,3.25,3.5,4,3.5,3.75,3.75,2.5,2.75,3,2.5,2.5,2.75,2.5,3.25,3,3.25,4,3.75,4,3,2.75,3,3.25,3.25,3.25,3.5,3,3.5,2.5,2.75,3,3.75,3.25,3.25,3.5,3.75,3.5,4,3.75,3.25,3.25,3.5,3.5,2.75,3.25,3.5,3.75,4,2.75,1.75,3.25,3.5,1.75,3.5,3.25,3.5,3.5,3.75,3.25,3,3.25,2.75,2.75,2.75,2.75,3.25,3.5,2.75,2.5,2.5,2.5,3.25,2.75,3,3.5,3.5,3.75,2.75,3,2.75,3.5,3,3.5,3.5,4,3.25,3.75,3.5,3.25,3.5,3.75,4,3.5,3,4,1.5,3,2.75,3.75,3.75,3.25,3,3.25,3.5,2.75,3.25,2.75,3.5,2.5,2.5,2.75,2.25,3,2,2,3,3,3.75,3.25,2.75,3,3.5,3.25,3,2.5,2.75,2.75,3,2.75,3.25,3.5,3.5,3.25,2.5,3.75,2.5,2.5,2.5,2.75,3,3,3.25,2.75,2.75,3.25,3,3,3.25,3.5,3.5,2.75,3.5,2.25,2.75,3.75,3.5,2.75,3.75,2.75,3.25,3.5,3.5,3,3.25,3.75,3.25,3.25,2.75,3.25,3.25,3.25,3,3.25,2.75,2.75,3,3,3,3,3,3.75,2.5,2.75,3.5,3.75,3.75,3.5,3.25,3.5,2.75,3,3.25,3,3.25,3.75,3.75,3.5,3,3.5,3.5,3.75,2.5,3.75,3.25,3.25,4,3.25,3.75,3.25,3.25,3.25,2,2.75,2.5,2.75,2.5,3.5,3.75,2.75,2.25,3,3.25,2.5,3.5,3.5,3,3.25,3.5,3.5,2,3.5,3.25,3.75,3.75,3.5,3.5,3.5,3.25,3.75,3,2.5,3,3.5,2.75,3.25,3.25,2.5,3.75,2.75,2.75,3.5,2.75,3.25,3.25,3,3,3,3,3.5,2.75,2.75,3.25,2.75,3,3,3.25,2,3.25,3,3.5,3.5,3.75,3.5,2.75,3,3.25,3.25,2.75,2.75,2.75,2.75,3,3,2.75,3.5,3.25,2.75,3,2.25,3.25,2.75,4,2.75,2.75,3.75,2.5,2.75,3.25,3.25,3.25,3.75,3,2.5,3.25,4,3.75,3.75,3,3,2.75,3.25,3.25,3.5,3,3.75,3,3.25,3,3.25,2.75,2.75,2.5,3.5,3.25,3.25,3.25,3,3.25,3.5,3.75,2.75,3,3.75,3.25,3.75,3,3,3.5,2,3.5,2.75,3.5,2.75,2.75,3.25,3.5,2.5,2.75,2.75,3.5,2.5,2.5,2.75,3.75,3.5,3.75,3.75,3.25,2.75,3,3.25,3.5,2.75,3,3.5,3.25,3,3,3.25,2.25,2.5,3,3.25,3.5,2.75,3,3.25,3.75,3,3.25,3.25,3,2.5,2.75,3,3.5,3.75,2,2,4,4,2,3.75,3.75,3.25,3.5,2.5,1.5,3.5,2.75,2.75,3,2.75,3.25,3.5,3.5,3.5,2.75,2.75,2.75,3.5,2.25,2.5,2.75,3.5,3,3,3.75,3,3,2.75,3,2,2.75,2.75,3,2.75,3,3,2.75,3.25,3.5,2.75,2.75,3.5,3.25,3.5,3,3.5,4,3,2.5,2.5,2.75,3.25,3.25,3.5,2.5,3.75,3.75,2.75,3.5,3.25,2,2,3.5,3.5,2.75,3,3.5,2.5,3.25,3.75,3.5,3.5,3.75,3.75,3.25,3.5,4,3.75,2.75,2.75,3.25,4,3.75,3.75,3,3.75,2,2.75,2.75,2,2.5,2.5,3.5,3.5,3.75,3,3,3.25,3.25,3.75,3,3.25,3.5,2.75,3,3.25,3.25,3.5,3.75,3.25,4,3.5,3.25,4,4,3.25,3.75,4,3.25,3.5,3.25,3.5,3.5,3.25,3.25,3.5,3,3,3.25,3.75,3.5,2.75,3,2.25,3,3,3.5,3.5,2.5,2.5,2.75,2.75,2.75,2.5,3,3.25,2.75,3,3.25,2.75,3,2.5,2.75,2.5,2.75,3,3.5,3.25,2,3.25,2.75,3,3.25,2.75,3,3.25,2.75,3.25,3.5,3.25,2.5,2.75,3,3,2.75,3.5,3.25,2.5,2.75,3.75,2.75,3,3.25,4,3.5,3.5,4,3.25,3.75,3.75,1.5,3.25,3.25,3.25,3.5,3.75,3.25,3,2.5,2.5,3,4,2.25,3.5,3.75,3,3.5,3.75,3.25,2.5,3,4,3.5,2.75,2.5,3,3.25,3.25,3,3.25,4,3.5,3.5,3.75,3.75,3,4,3.75,3.25,2.5,3.5,3.5,3.5,3,3.5,2.75,3.75,3.5,3.5,3.75,3.5,3,3,3,3.5,2,3,3.5,3.25,4,4,3.5,3,2,3,3,2.75,3.5,3.75,3,3.75,3.75,3,3,4,2.75,3,3,4,3.5,2.5,3.75,2.5,3,2.5,2.75,2.5,2.75,2.5,2.5,2.75,3,3,2.75,4,3.5,3.75,3.75,3.25,3.5,3,2.75,2.5,3.5,3.5,3,3.5,3.5,3.5,3.25,3.5,2.75,3.25,3.5,2,2.5,2.25,3,3,3,2.75,3,3.5,3.75,3.5,3.5,3.5,3.25,3.75,3,2.75,3.5,3.25,2.75,3.75,4,3.5,3.75,3.75,3,3,4,3,3,3,4,2.5,3.5,3.25,3.25,3,3.5,3.5,4,2.5,3,3.5,3.75,3.5,3.5,3.5,2.75,2.75,3,3,3.5,2.75,4,3.5,3.25,4,4,3,3.25,3.75,3.5,3.5,3.75,3.5,3,3.5,2.75,2.5,3.25,3,2.75,2.75,2.5,3,3.75,3.75,2.5,3.25,3.5,3.5,3.5,3,3.5,3,3.75,3,4,3,3.5,3.25,2.75,2.75,4,3.5,2.75,1.5,3.25,3.75,3,3.5,2,2.5,3.75,3.25,3,3.5,2.5,2.75,2.5,2.75,3.75,3,2.5,3.5,2,2,3,3,4,3.5,3.75,4,3.5,3.75,3.25,3.75,3.5,2.75,3.5,3,3.5,3.5,3.5,3,3,3.5,2.5,3,2.75,3.5,3.5,2.5,3.25,3,2.75,3,2.75,3.75,3.5,3,3,3.5,3.5,3,3.5,3,3.75,3.5,3.25,3.75,4,2.75,2.75,3,2.5,3,3,3.5,3,3.25,3,3.5,3.5,3.75,3.25,3.75,3.25,2.75,3.25,2.75,3.75,3.75,3.75,3.75,2.5,3.75,3.25,3,3.25,3.5,3,2.75,2.75,3.5,3.75,3.5,3.25,3.75,4,4,2.75,3.75,4,3,3.5,3.25,3,3.25,3.25,3.5,3.25,4,3.5,2.75,3,3.5,3.25,3.75,2.5,2.5,2.75,3,2.75,3,3,3.5,3.25,3,3,2.5,3.5,3.5,2.75,2.75,3,3.25,2.75,2.5,2.75,3.5,3.25,3.5,3.25,3,3.25,2.75,2.5,3,4,4,4,3,3.25,3.5,2.75,3,3.25,4,2.75,3,3.25,3.75,3.25,3.25,3,3,3.5,3,4,1.75,3,3.5,3,3,3.25,2.75,3.5,3,3.75,3.5,3.25,3.5,2,3.5,3.5,3,3,3.25,3.25,3,3.75,3.25,3.5,3.5,2.75,2.75,3.75,3.5,3,3,3.5,3.25,3.5,2.75,2.5,2.75,3,3,2.75,3.5,3.25,3,3.5,4,3.5,3.5,3.25,3.25,3,2.75,3,2.5,2.75,3,3,2.75,3.5,2.5,2.75,2.75,2.75,3,3,3.25,3.75,3.75,3.75,4,3.5,3.5,4,2.25,2.75,2.75,3.25,2.5,3.5,3,3.5,3.5,4,3,3,3.5,3,3.5,2.75,3.25,3.25,3.75,4,3.5,3.5,3.5,3.75,3.5,2.75,2.75,3,3.5,3.75,2,3.25,3,3,3.75,3.75,3,3,3,3.5,3.75,4,3.25,3.75,3.75,4,3.5,3.75,3.5,2.75,3,3.5,3,2.75,3.25,3,3.75,3.75,3.75,3.75,3.5,4,3.5,3,3.75,3,2.5,3.5,3.5,3.5,2.75,2.75,3,3,3.25,3.5,3.25,3.25,3.5,3,3.5,2.5,3.5,3.25,3.75,3,3,3.75],"type":"box","line":{"fillcolor":"rgba(31,119,180,1)","color":"rgba(31,119,180,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1}},"base_url":"https://plot.ly"},"evals":["config.modeBarButtonsToAdd.0.click"],"jsHooks":{"render":[{"code":"function(el, x) { var ctConfig = crosstalk.var('plotlyCrosstalkOpts').set({\"on\":\"plotly_click\",\"persistent\":false,\"dynamic\":false,\"selectize\":false,\"opacityDim\":0.2,\"selected\":{\"opacity\":1}}); }","data":null}]}}</script><!--/html_preserve-->

I had assumed that the different country would have better ratings than the others, but my assumption is that the outliers may have actually had slightly lower rater chocolate. Three of the countries had a median rating of 3.25 while the U.K. had a slightly lower median rating could be the different population. The other possiblity is that Canada is the different population since their chocolate has a higher third quantile than the others.


```r
pander(choc.loc %>%
  group_by(Location) %>%
  summarize( Num_of_Samples= n(),
             Median_Rating= median(Rating),
             Max_Rating= as.integer(max(Rating)),
             Third_Quantile= quantile(Rating,.75)))
```


-------------------------------------------------------------------------
 Location   Num_of_Samples   Median_Rating   Max_Rating   Third_Quantile 
---------- ---------------- --------------- ------------ ----------------
  Canada         124             3.25            4             3.75      

  France         156             3.25            4            3.562      

   U.K.           96               3             4             3.5       

  U.S.A.         764             3.25            4             3.5       
-------------------------------------------------------------------------

While this indicates that the U.K. produces lower quality chocolate than Canada, France and the United States, the Kruskal-Wallis test has revealed only that this data is worth further investigation. There is no way to know more without a deeper and more specific analysis.

<div>
