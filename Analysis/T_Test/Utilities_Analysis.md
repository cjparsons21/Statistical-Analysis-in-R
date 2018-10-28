---
title: "Utilities Dataset t Test"
output: 
  html_document:
    theme: cerulean
    code_folding: hide
    keep_md: TRUE
---


```r
# Note that the include=FALSE makes it so that this chunk doesn't show up in the knitted document.
library(mosaic)
library(pander)
library(DT)
library(plotly)
library(tidyverse)
```



```r
z_kwh<-(Utilities$kwh-mean(Utilities$kwh))/sd(Utilities$kwh)
z_ccf<-(Utilities$ccf-mean(Utilities$ccf))/sd(Utilities$ccf)
```

## Question
Going through the Utilities data frame I came to the following question. 
"Does the z-score of gas used differ from the z-score of electricity?" 
I decided to investigate this information because I thought that people who use both gas and electricity use different amounts throughout the year and I wanted to see if people use more of one than the other. Since gas and electricity use different units, I measured the difference of the z-Scores. Gas and electricity are paired for each measured day, making a two sided paired t-Test the most effective test to use. This data has a sufficiently large sample size to be normally distributed and the test statistic being used is α=.05.



$$
  H_0: \mu_\text{z-score of kwh} - \mu_\text{z-score of ccp} = 0
$$
$$
  H_a: \mu_\text{z-score of kwh} - \mu_\text{z-score of ccp} \neq 0
$$



## Results
Below are the results of a t-Test on this information:

```r
pander(t.test(z_kwh, z_ccf, paired=T))
```


-----------------------------------------------------------------------------------
 Test statistic   df    P value   Alternative hypothesis   mean of the differences 
---------------- ----- --------- ------------------------ -------------------------
   -1.767e-15     116      1            two.sided                -2.208e-16        
-----------------------------------------------------------------------------------

Table: Paired t-test: `z_kwh` and `z_ccf`

This t-Test led to some very concerning result. The P value was 1, meaning that I must fail to reject the null hypothesis. However, a P value of 1 is so improbable that I likely did something in my analysis and use of the data to cause this problem. Based on these results, I will need to investigate a different question.

Below is a histogram showing the differerences of the z-Scores for gas and electricity use from the data frame. Since the electricity and gas uses are measured in different units and in vastly different quantities, the best way to measure how different they are is to compare the z-scores. Considering the t-Test results, the majority of the results should be grouped around 0.




```r
ggplot()+
  geom_histogram(aes(x = z_kwh - z_ccf), binwidth = .65, 
                 fill = "darkslategray4", color = "black")+
  xlab('Difference in Gas and Electricity Z-Scores')+
  ylab("Frequency")+
  ggtitle("Difference beteen Yearly Gas and Electricity Use") +
  theme_linedraw()
```

![](Utilities_Analysis_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
  ggsave("Difference beteen Yearly Gas and Electricity Use.png")
```

The next thing I should investigate is whether the gas and electricity uses average out over the course of the year but are used in varying amounts depending on the season, or if the consistenly follow each other.



