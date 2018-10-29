---
title: "Simple Logistic Regression of New Intakes"
output: 
  html_document:
    theme: cerulean
    code_folding: hide
    keep_md: TRUE
---

```r
library(ResourceSelection) 
library(mosaic)
library(pander)
library(ggplot2)
library(plotly)
```


```r
PC_Data <- read.csv("~/Documents/Math_325/Data/PC_Data.csv", header = TRUE, sep = ",")
```

The following analysis is a simple logistic regression of the probability that a clinician will have a new intake based on how many appointments (excluding intakes) they have in a given week. The following goodness of fit test verifies the normality of the data with an α level of .05 which indicates a failure to reject the null based on a P-value of 0.1611. 



```r
PCD.glm <- glm(Intake>0 ~ Progress_Total, data=PC_Data, family=binomial)

pander(hoslem.test(PCD.glm$y, PCD.glm$fitted, g=10))
```


-------------------------------
 Test statistic   df   P value 
---------------- ---- ---------
     11.78        8    0.1611  
-------------------------------

Table: Hosmer and Lemeshow goodness of fit (GOF) test: `PCD.glm$y, PCD.glm$fitted`

This logistic regression will utilize the following model and hypothesis:

$$
  P(Y_i = 1|x_i) = \frac{e^{\beta_0+\beta_1 x_i}}{1+e^{\beta_0 + \beta_1 x_i}} = \pi_i
$$
A brief explaination of β1 is the this is the slope of the line. A β1 of zero means that there is no relationship between the two variables in question and one is incapable of indicating the other.

The alternative hypothesis states that there is a relationship between the two and that the regression has a slope. (This is a good thing)

$$
  H_0: \beta_1 = 0 \\
  H_a: \beta_1 \neq 0
$$


$$
  α = .05
$$

A logistic regression analysis of the relationship between the number of intakes and how many clients are seen a week (excluding intakes) has provided the following results with a p-value of 0.0027. This means that there is enough evidence to reject the null hypothesis that there no slope. 


```r
pander(summary(PCD.glm))
```


-----------------------------------------------------------------
       &nbsp;         Estimate   Std. Error   z value   Pr(>|z|) 
-------------------- ---------- ------------ --------- ----------
  **(Intercept)**     0.06226      0.2761     0.2255     0.8216  

 **Progress_Total**   0.04315     0.01438        3       0.0027  
-----------------------------------------------------------------


(Dispersion parameter for  binomial  family taken to be  1 )


-------------------- ---------------------------
   Null deviance:     388.1  on 315  degrees of 
                               freedom          

 Residual deviance:   378.7  on 314  degrees of 
                               freedom          
-------------------- ---------------------------

The following chart is an interactive visual representation of the probability that a clinician will gain at least one new intake that week. You can hover over any point of the line and see the number of patients seen in a week in relation to the probability of taking on a new client. 1 is a guaranteed client and 0 is the guarantee that no client will be gained.


```r
#plot( Intake>0 ~ Progress_Total, data=PC_Data, main="", ylab='', pch=16)
#curve(exp(0.06226+0.04315*x)/(1+exp(0.06226+0.04315*x)), from=-2, to=48, add=TRUE)

PCD.gg <- ggplot(PC_Data, aes(x=PC_Data$Progress_Total, y=as.numeric(Intake>0)) ) + 
  geom_point() + 
  stat_smooth(method="glm", method.args= list(family="binomial"), se=FALSE)+
  ggtitle("Logistic Regression") +
  xlab("Number of Appointments per Week (Excluding Intakes)") + ylab("Probability of an Intake")
ggsave("LogisticRegression.png")

PCD.gg.ply <- plotly_build(PCD.gg)

PCD.text=paste("", "",  sep="")    
style( PCD.gg.ply, text=PCD.text, hoverinfo = "Axis", traces = c(1, 2) )
```

<!--html_preserve--><div id="73592269b54b" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="73592269b54b">{"x":{"data":[{"x":[13,9,4,0,17,16,23,2,10,21,15,18,13,16,11,14,21,21,25,31,11,15,22,13,24,10,29,23,16,15,13,13,16,10,23,25,22,6,22,21,27,14,34,26,15,24,24,24,24,16,22,15,0,9,23,22,28,21,19,29,11,0,22,11,19,11,15,22,26,27,19,18,25,21,25,25,31,26,10,30,16,30,18,14,16,27,17,32,29,25,30,32,31,20,15,22,32,13,28,31,32,29,20,26,23,22,7,27,25,21,29,26,26,32,17,31,16,30,17,34,19,26,25,26,29,35,27,32,30,31,29,12,40,30,35,24,12,19,34,16,40,34,30,39,35,40,29,24,30,38,23,36,44,39,33,23,38,21,20,0,19,19,19,15,9,0,14,15,13,12,10,9,13,8,22,22,24,0,20,20,16,19,16,17,9,20,19,18,16,8,8,13,14,23,21,17,16,23,23,23,10,16,21,16,21,20,14,22,20,21,0,19,9,18,11,17,13,19,18,0,2,4,5,5,6,4,7,8,11,9,15,17,16,15,15,17,18,16,23,17,24,21,19,21,12,19,22,19,20,6,14,20,16,26,20,28,25,25,25,24,18,22,23,18,26,29,18,27,25,24,23,16,0,0,0,0,0,3,9,9,12,10,2,8,15,9,15,9,13,14,7,14,10,15,16,14,14,5,10,17,10,13,0,11,12,10,8,15,11,7,11,15,15,11,6,15],"y":[1,1,0,0,1,0,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,0,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,0,0,0,1,1,1,0,1,1,0,0,1,0,1,1,0,0,0,0,0,0,1,1,1,1,1,1,1,0,1,1,1,1,1,1,0,0,1,1,1,1,0,0,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,0,0,1,0,1,1,1,1,1,1,1,0,0,0,1,1,0,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,0,0,1,1,0,1,1,1,1,1,1,0,1,1,0,1,0,1,1,1,1,0,1,1,0,0,0,0,0,1,0,0,1,0,1,1,0,0,1,0,1,1,1,0,1,1,0,0,1,0,1,1,1,0,1,1,1,0,1,1,1,1,0,1,1,0,0,1,1,0,1,0,0,0,1,1,1,1,1,1,1,0,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,0,1,1,1,1,1,0,1,1,1,1,1,1,1,0,1,1,0,0,0,1,1,0,1,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,0,0,0,1,1,1,0,1,1,0,1,1,1,0,0,0,1,0,0,0],"text":"","type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"Axis","frame":null},{"x":[0,0.556962025316456,1.11392405063291,1.67088607594937,2.22784810126582,2.78481012658228,3.34177215189873,3.89873417721519,4.45569620253165,5.0126582278481,5.56962025316456,6.12658227848101,6.68354430379747,7.24050632911392,7.79746835443038,8.35443037974684,8.91139240506329,9.46835443037975,10.0253164556962,10.5822784810127,11.1392405063291,11.6962025316456,12.253164556962,12.8101265822785,13.3670886075949,13.9240506329114,14.4810126582278,15.0379746835443,15.5949367088608,16.1518987341772,16.7088607594937,17.2658227848101,17.8227848101266,18.379746835443,18.9367088607595,19.4936708860759,20.0506329113924,20.6075949367089,21.1645569620253,21.7215189873418,22.2784810126582,22.8354430379747,23.3924050632911,23.9493670886076,24.506329113924,25.0632911392405,25.620253164557,26.1772151898734,26.7341772151899,27.2911392405063,27.8481012658228,28.4050632911392,28.9620253164557,29.5189873417721,30.0759493670886,30.6329113924051,31.1898734177215,31.746835443038,32.3037974683544,32.8607594936709,33.4177215189873,33.9746835443038,34.5316455696203,35.0886075949367,35.6455696202532,36.2025316455696,36.7594936708861,37.3164556962025,37.873417721519,38.4303797468354,38.9873417721519,39.5443037974684,40.1012658227848,40.6582278481013,41.2151898734177,41.7721518987342,42.3291139240506,42.8860759493671,43.4430379746835,44],"y":[0.515560398944261,0.521560130191682,0.527553647554398,0.533539232996363,0.539515177599168,0.545479783502005,0.551431365814758,0.557368254499674,0.563288796217215,0.569191356131832,0.575074319673561,0.580936094251526,0.586775110915633,0.592589825962927,0.598378722485336,0.60414031185572,0.609873135149398,0.615575764498594,0.621246804377448,0.626884892815548,0.632488702538162,0.638056942031644,0.643588356532723,0.649081728940687,0.65453588065169,0.659949672314717,0.665322004508963,0.670651818342655,0.675938095973566,0.681179861051739,0.686376179085126,0.691526157729104,0.69662894700101,0.701683739421048,0.706689770081098,0.711646316643141,0.716552699269165,0.72140828048457,0.726212464977229,0.730964699334476,0.735664471720407,0.740311311495984,0.744904788784491,0.749444513984997,0.753930137236502,0.758361347835514,0.762737873609833,0.767059480251325,0.771325970610503,0.775537183955709,0.779692995199707,0.783793314096442,0.787838084410735,0.791827283063602,0.795760919255873,0.799639033572729,0.803461697071682,0.807229010356523,0.810941102639622,0.814598130794944,0.818200278404023,0.82174775479709,0.825240794091433,0.828679654228997,0.832064616015132,0.835395982160313,0.838674076326542,0.841899242180083,0.845071842452039,0.848192258008236,0.85126088692974,0.854278143605276,0.857244457836692,0.860160273958557,0.863026049972864,0.865842256699734,0.868609376944933,0.871327904684928,0.873998344270143,0.876621209646959],"text":"","type":"scatter","mode":"lines","name":"fitted values","line":{"width":3.77952755905512,"color":"rgba(51,102,255,1)","dash":"solid"},"hoveron":"points","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"Axis","frame":null}],"layout":{"margin":{"t":43.7625570776256,"r":7.30593607305936,"b":40.1826484018265,"l":48.9497716894977},"plot_bgcolor":"rgba(235,235,235,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":"Logistic Regression","titlefont":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"xaxis":{"domain":[0,1],"type":"linear","autorange":false,"range":[-2.2,46.2],"tickmode":"array","ticktext":["0","10","20","30","40"],"tickvals":[0,10,20,30,40],"categoryorder":"array","categoryarray":["0","10","20","30","40"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":null,"gridwidth":0,"zeroline":false,"anchor":"y","title":"Number of Appointments per Week (Excluding Intakes)","titlefont":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"type":"linear","autorange":false,"range":[-0.05,1.05],"tickmode":"array","ticktext":["0.00","0.25","0.50","0.75","1.00"],"tickvals":[0,0.25,0.5,0.75,1],"categoryorder":"array","categoryarray":["0.00","0.25","0.50","0.75","1.00"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":null,"gridwidth":0,"zeroline":false,"anchor":"x","title":"Probability of an Intake","titlefont":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":false,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.88976377952756,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":[{"name":"Collaborate","icon":{"width":1000,"ascent":500,"descent":-50,"path":"M487 375c7-10 9-23 5-36l-79-259c-3-12-11-23-22-31-11-8-22-12-35-12l-263 0c-15 0-29 5-43 15-13 10-23 23-28 37-5 13-5 25-1 37 0 0 0 3 1 7 1 5 1 8 1 11 0 2 0 4-1 6 0 3-1 5-1 6 1 2 2 4 3 6 1 2 2 4 4 6 2 3 4 5 5 7 5 7 9 16 13 26 4 10 7 19 9 26 0 2 0 5 0 9-1 4-1 6 0 8 0 2 2 5 4 8 3 3 5 5 5 7 4 6 8 15 12 26 4 11 7 19 7 26 1 1 0 4 0 9-1 4-1 7 0 8 1 2 3 5 6 8 4 4 6 6 6 7 4 5 8 13 13 24 4 11 7 20 7 28 1 1 0 4 0 7-1 3-1 6-1 7 0 2 1 4 3 6 1 1 3 4 5 6 2 3 3 5 5 6 1 2 3 5 4 9 2 3 3 7 5 10 1 3 2 6 4 10 2 4 4 7 6 9 2 3 4 5 7 7 3 2 7 3 11 3 3 0 8 0 13-1l0-1c7 2 12 2 14 2l218 0c14 0 25-5 32-16 8-10 10-23 6-37l-79-259c-7-22-13-37-20-43-7-7-19-10-37-10l-248 0c-5 0-9-2-11-5-2-3-2-7 0-12 4-13 18-20 41-20l264 0c5 0 10 2 16 5 5 3 8 6 10 11l85 282c2 5 2 10 2 17 7-3 13-7 17-13z m-304 0c-1-3-1-5 0-7 1-1 3-2 6-2l174 0c2 0 4 1 7 2 2 2 4 4 5 7l6 18c0 3 0 5-1 7-1 1-3 2-6 2l-173 0c-3 0-5-1-8-2-2-2-4-4-4-7z m-24-73c-1-3-1-5 0-7 2-2 3-2 6-2l174 0c2 0 5 0 7 2 3 2 4 4 5 7l6 18c1 2 0 5-1 6-1 2-3 3-5 3l-174 0c-3 0-5-1-7-3-3-1-4-4-5-6z"},"click":"function(gd) { \n        // is this being viewed in RStudio?\n        if (location.search == '?viewer_pane=1') {\n          alert('To learn about plotly for collaboration, visit:\\n https://cpsievert.github.io/plotly_book/plot-ly-for-collaboration.html');\n        } else {\n          window.open('https://cpsievert.github.io/plotly_book/plot-ly-for-collaboration.html', '_blank');\n        }\n      }"}],"cloud":false},"source":"A","attrs":{"73595977a7ee":{"x":{},"y":{},"type":"scatter"},"73593eb22825":{"x":{},"y":{}}},"cur_data":"73595977a7ee","visdat":{"73595977a7ee":["function (y) ","x"],"73593eb22825":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1}},"base_url":"https://plot.ly"},"evals":["config.modeBarButtonsToAdd.0.click"],"jsHooks":{"render":[{"code":"function(el, x) { var ctConfig = crosstalk.var('plotlyCrosstalkOpts').set({\"on\":\"plotly_click\",\"persistent\":false,\"dynamic\":false,\"selectize\":false,\"opacityDim\":0.2,\"selected\":{\"opacity\":1}}); }","data":null},{"code":"function(el, x) { var ctConfig = crosstalk.var('plotlyCrosstalkOpts').set({\"on\":\"plotly_click\",\"persistent\":false,\"dynamic\":false,\"selectize\":false,\"opacityDim\":0.2,\"selected\":{\"opacity\":1}}); }","data":null},{"code":"function(el, x) { var ctConfig = crosstalk.var('plotlyCrosstalkOpts').set({\"on\":\"plotly_click\",\"persistent\":false,\"dynamic\":false,\"selectize\":false,\"opacityDim\":0.2,\"selected\":{\"opacity\":1}}); }","data":null}]}}</script><!--/html_preserve-->

Note that a week with no clients seen still has over a 50% chance of a new intake. The fraction immediately below has the probability of a new intake with no clients seen that week. The one below that shows the probability of an intake with 45 patients.



```r
pander(predict(PCD.glm, data.frame(Progress_Total=0), type='response'))
```


--------
   1    
--------
 0.5156 
--------

```r
pander(predict(PCD.glm, data.frame(Progress_Total=45), type='response'))
```


--------
   1    
--------
 0.8812 
--------

Note that this test is called a "simple" logistic regression for a reason. This is a surface level analysis which does not take into account various factors which may be influencing this relationship. It may be that clinicians who see more patients work more or are trusted more are more likely to have more intakes. It may also be that more patients seen in a week leads to more outside people being willing to come in for an intake appointment. While the deeper factors simply can't be verified by this test, there is a proven relationship between clients seen in a week and the chance of a new intake being seen by that clinician.





