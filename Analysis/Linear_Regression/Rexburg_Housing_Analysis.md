---
title: "Student Housing Costs"
output:
  html_document:
    code_folding: hide
    keep_md: TRUE
    theme: cerulean
    toc: yes
    toc_float: yes
  pdf_document:
    toc: yes
---



<br />

## Background

Stephanie[^1] is a student that will be starting school at BYU-Idaho next semester. Here is a copy of the email request for information that she sent.

"Hi. My name is Stephanie. I would like to learn about what housing options I have for living at BYU-Idaho next semester. It will be my first semester there, so I would like to find something that is close to campus and around $300 a month in rent. I'm not too picky on roomates, but I would like somewhere that has a lot of people around so I can get to know as many people as possible. Thanks in advance!"


<!-- Hint, you should probably subset the Rent dataset to only show the "Approved Women's Housing" since Stephanie won't be interested in Male Housing options. Also, you can edit this background however you wish when completing your analysis. Notice how this text in green doesn't show up in the HTML output because we used the HTML comment tags around this text. --> 

## Response to Question
Stephanie, after considering your request I have made the following chart for your use in deciding where you would most like to live in Rexburg. There are 57 total Women's apartments in Rexburg, but not all of them fit into what you are looking for.

With a budget of 300 dollars each month for housing you will spend a total of 1050 dollars each semester as each semester is 3.5 months long. You requested somewhere to live close to campus, so I looked for housing within a five minute walk of campus. You also wanted to live somewhere with lots of other students around, so I looked for apartment complexes with at least 50 students. These are the complexes I found.

The following chart shows all of the Approved Women's Housing in blue with my recommendations in orange. Each dot represents an apartment complex with the size of the dot increasing based on how many people live in each complex. You may scroll over each point to see specifics on each apartment complex.


```r
library(plotly)
#plot_ly() %>%
#  add_trace(data=w_rent,x=~WalkMinutes,y=~Cost, type="scatter",
#            size=~Residents,text=~paste(Apartment,'<br># Residents: ',Residents),
#            name='All Approved Housing') %>%
#  add_trace(data=ideal,x=~WalkMinutes,y=~Cost, type="scatter",
#            size=~Residents,text=~paste(Apartment,'<br># Residents: ',Residents),
#            name='Housing within Criteria') %>%
#  layout(title='Approved Women\'s Housing',
#         yaxis=list(title='Cost per Semester'),
#         xaxis=list(title='Distance from Campus (in Minutes)'),
#        legend=list(x=.6,y=.9))

  ggplot() +
  geom_point(data = w_rent, aes( x = WalkMinutes, y = Cost), color = "blue") +
  geom_point(data = ideal, aes( x = WalkMinutes, y = Cost), color = "orange") +
    labs(title = "Approved Women's Housing", 
         x = "Distance from Campus (in Minutes)", 
         y = "Cost per Semester")
```

![](Rexburg_Housing_Analysis_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

```r
  ggsave("Approved Women's Housing.png")
```




Of all of the apartments in Rexburg, only 7 fit within the given constraints. You have many other housing options near campus and the largest cluster of apartments are all within a 6 minute walk of campus and under $1,100. However, not all meet your desire to live in the same complex as many other students.

```r
chosen<-select(ideal,Apartment,Phone,Cost,Residents)
pander(head(chosen,n=7))
```


---------------------------------------------------------------
         Apartment               Phone        Cost   Residents 
--------------------------- ---------------- ------ -----------
 ALLEN'S SUNRISE VILLAGE -    208-356-3995    1000      144    
           WOMEN                                               

        BIRCH PLAZA          (208) 359-8200   980       343    

       BIRCH WOOD I          (208) 359-8200   1019      65     

      CARRIAGE HOUSE          208-356-4361    1075      234    

     COTTONWOOD-WOMEN        (208) 356-9576   950       84     

   DAVENPORT APARTMENTS       208-356-3216    895       71     

        ROYAL CREST           208-356-7778    995       342    
---------------------------------------------------------------

While this analysis was done for an assignment, it was interesting to notice that there seems to be little to no relationship between how far away an apartment complex is from campus and their price. This means an investor could build a complex further away from campus where it is cheaper and still charge as much anyone else, if not more. This claim can be confirmed with the linear regression model below where WalkMinutes is used to predict Cost. The R squared value is practically 0 and the P value is far too high to confirm any reasonable confidence level.


```r
housing_lm <- lm(data = w_rent, Cost ~ WalkMinutes)
summary(housing_lm)
```

```
## 
## Call:
## lm(formula = Cost ~ WalkMinutes, data = w_rent)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -280.20 -140.54   -9.22  124.36  441.32 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 1104.587     49.542  22.296   <2e-16 ***
## WalkMinutes    6.516      7.475   0.872    0.387    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 181.9 on 52 degrees of freedom
##   (3 observations deleted due to missingness)
## Multiple R-squared:  0.0144,	Adjusted R-squared:  -0.004551 
## F-statistic: 0.7599 on 1 and 52 DF,  p-value: 0.3874
```






[^1]: Note that Stephanie is a fictional character who is based on real experiences of many faculty and staff here at BYU-Idaho.
