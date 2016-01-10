---
title: "2016 MLB Predictions - Chicago Teams"
author: "Kurt Schuepfer"
date: "January 08, 2016"
output: html_document
---

# Introduction

Sabermetrics is the empirical analysis of baseball. Baseball has always been a game of statistics. But with the explosion of data in the [past 7 years](http://www.datanami.com/2014/10/24/todays-baseball-analytics-make-moneyball-look-like-childs-play/), in conjunction with the development of more advanced analytics techniques, baseball teams now - more than ever - can leverage their numbers in increasingly sophisticated ways. There is so much data available today that just about anyone - with the proper tools and training at their disposal - can be a part of the action. 

<img src="http://2s7gjr373w3x22jf92z99mgm5w.wpengine.netdna-cdn.com/wp-content/uploads/2014/10/basebal-data-growth.png" width="500px" height="100px" />  

Source: http://www.datanami.com/2014/10/24/todays-baseball-analytics-make-moneyball-look-like-childs-play/

In this report, I use linear regression to predict a number of outcomes for both the Chicago Cubs and the Chicago White Sox in 2016. After extrapolating from just a few linear models, I found projections that:  

* The Chicago White Sox will win 87 games in 2016 and will NOT make the playoffs.  
* The Chicago Cubs will win 108 games in 2016 and WILL make the playoffs.

##Loading the Data
First, we'll load in a dataset of MLB statistics from 1962-2015. 

```r
baseball <- read.csv("baseball.csv")
str(baseball)
```

```
## 'data.frame':	1322 obs. of  15 variables:
##  $ Team        : Factor w/ 39 levels "ANA","ARI","ATL",..: 2 3 4 5 7 8 9 10 11 12 ...
##  $ League      : Factor w/ 2 levels "AL","NL": 2 2 1 1 2 1 2 1 2 1 ...
##  $ Year        : int  2015 2015 2015 2015 2015 2015 2015 2015 2015 2015 ...
##  $ RS          : int  720 573 713 748 689 622 640 669 737 689 ...
##  $ RA          : int  713 760 693 753 608 701 754 640 844 803 ...
##  $ W           : int  79 67 81 78 97 76 64 81 68 74 ...
##  $ OBP         : num  0.324 0.314 0.307 0.325 0.321 0.306 0.312 0.325 0.315 0.328 ...
##  $ SLG         : num  0.414 0.359 0.421 0.415 0.398 0.38 0.394 0.401 0.432 0.42 ...
##  $ BA          : num  0.264 0.251 0.25 0.265 0.244 0.25 0.248 0.256 0.265 0.27 ...
##  $ Playoffs    : int  0 0 0 0 1 0 0 0 0 0 ...
##  $ RankSeason  : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ RankPlayoffs: int  NA NA NA NA 5 NA NA NA NA NA ...
##  $ G           : int  162 162 162 162 162 162 162 162 162 162 ...
##  $ OOBP        : num  0.322 0.339 0.321 0.327 0.29 0.322 0.328 0.297 0.353 0.33 ...
##  $ OSLG        : num  0.423 0.426 0.418 0.424 0.372 0.41 0.42 0.386 0.461 0.444 ...
```

The dataset contains many variables related to historical team performance. The table below describes each variable:

Variable Name | Variable Description
------------- | -------------
Team | MLB team abbreviations (e.g. ARI = Arizona Diamondbacks)
League | American League (AL) or National League (NL)
Year | Year
RS | Runs scored by team's batters
RA | Runs allowed by team's pitchers
W | Total wins in regular season
OBP | On-base percentage: a measure of how often a batter reaches base
SLG | Slugging percentage: a measure of the power of a hitter (total bases/total at bats)
BA | Batting average: a measure of the average performance of a batter (hits/total at bats)
Playoffs | Dichotomous variable representing whether or not a team made the playoffs
RankSeason | How each team ranked overall in the regular season
RankPlayoffs | How each playoff team ranked in the playoffs
G | Total games played
OOBP | Opponent's OBP: a measure of how often a team lets the opponents reach base
OSLG | Opponent's SLG: a measure of how far along the bases a team allows the opponent to get


***
##Framing the Analysis
The primary goal of this report is to determine whether either of the Chicago baseball teams will make it to the playoffs in 2016. To do this, it would be helpful to know how many regular season games a team must typically win to get to the playoffs.

###Making it to the playoffs

Graphs shows that historically, if a team wins 94 or more games, they have a strong chance of making it to the playoffs. 

![Image of playoff wins](http://static5.businessinsider.com/image/4daf81594bd7c8a060200000/mlb-playoffs-win-totals.jpg)

[Click here](http://www.businessinsider.com/chart-of-the-day-what-it-takes-to-make-the-playoffs-in-baseball-2011-4) for more information about this chart.

###Winning 94 games
So how does a team win games?  

A baseball win happens when you outscore your opponent. We will create a variable, "RD" (runs differential) to represent the extent to which a team outscores their opponent. RD is simply calculated by subtracting a team's runs allowed from their runs scored. 


```r
baseball$RD <- baseball$RS-baseball$RA
```

Indeed, historically there is a *strong* relationship between RD and wins.
![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

The graph shows a clear linear pattern. But let's put some actual numbers to this relationship.

###Building a Model




We will build a linear model to predict wins with runs differential.

```r
winsReg <- lm(W ~ RD, data = baseball)
summary(winsReg)
```

```
## 
## Call:
## lm(formula = W ~ RD, data = baseball)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -14.4023  -2.7977   0.0735   2.8746  12.7747 
## 
## Coefficients:
##              Estimate Std. Error t value            Pr(>|t|)    
## (Intercept) 80.910741   0.110676  731.06 <0.0000000000000002 ***
## RD           0.104284   0.001082   96.39 <0.0000000000000002 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.024 on 1320 degrees of freedom
## Multiple R-squared:  0.8756,	Adjusted R-squared:  0.8755 
## F-statistic:  9290 on 1 and 1320 DF,  p-value: < 0.00000000000000022
```

The model summary indicates there is is a *strong* linear relationship between runs differential and wins. The p-value is highly significant, and the model produced an R^2 value that is typically considered high.  

###Using the Model
So now we've seen with both a chart and a model summary that RD is a good predictor of regular season wins. We can now use our model to compute just how big the runs differential must be for a team to meet the 94-game threshold.

Remember our summary showed us that:  

* Wins = 80.910741 + 0.104284(RD)  
 
Thus, for a team to have a strong chance of making it to the playoffs, the value of (80.910741 + 0.104284*RD) must be greater than or equal to 94.  

Do some simple arithmetic: 

```r
thresholdRD <- (94-80.910741)/0.104284
thresholdRD
```

```
## [1] 125.5155
```

**To win 94 games and have a strong chance of making the playoffs, in the regular season a team must outscore their opponents by 126 runs.**


***
***
#Doing the Analysis
Now that we have a couple of key figures in hand, let's proceed with the actual analysis.  

Remember, we want to determine if either of the Chicago baseball teams will make it to the playoffs this year. We know that if a team's RD is at least 126, they will probably win 94 games and get to go to the playoffs. 

So how can we predict the RD for each team in 2016? We will predict how many runs each team will score (this can be done with batting statistics) and then how many runs each team will allow (this can be done with pitching statistics).    


***
##Building a Model for Runs Scored
**Runs scored (RS)** can be predicted by OBP (on base %, includes walks) and SLG (how far a player gets around the bases at his at-bat; this measures a batter's power).  

Many teams used to focus on batting average. However OBP is [well-known](http://newenglishd.com/2013/07/23/why-obp-should-be-your-new-batting-average/) to be the more important predictor. Baseball analysts also consider SLG to be [important](http://www.amazinavenue.com/2010/12/9/1865359/sabermetrics-and-you-the-big-three-part-1-batting-average). 

Let's confirm this with a regression model.

```r
runsReg <- lm(RS ~ OBP + SLG + BA, data = baseball)
summary(runsReg)
```

```
## 
## Call:
## lm(formula = RS ~ OBP + SLG + BA, data = baseball)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -79.400 -16.779  -1.008  16.935  93.445 
## 
## Coefficients:
##             Estimate Std. Error t value            Pr(>|t|)    
## (Intercept)  -811.41      16.66 -48.707 <0.0000000000000002 ***
## OBP          2941.02      94.69  31.061 <0.0000000000000002 ***
## SLG          1525.85      36.58  41.708 <0.0000000000000002 ***
## BA           -154.84     111.65  -1.387               0.166    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 25.42 on 1318 degrees of freedom
## Multiple R-squared:  0.9212,	Adjusted R-squared:  0.921 
## F-statistic:  5137 on 3 and 1318 DF,  p-value: < 0.00000000000000022
```

**Note**: Both OBP and SLG are significant predictors of runs scored. BA is not. Moreover, note that BA is also negative, meaning that the lower the BA, the more runs scored. This obviously makes no sense. In statistics, when this happens, it usually means there is multicollinearity in the data (model summaries typically generate the amount of *unique* variance accounted for by each of the predictors. Because OBP, SLG, and BA are all likely correlated with one another, once OBP and SLG have accounted for their unique portion of the variability, there is very little left over for BA to explain). Thus, for this reason, we can simplify our model by removing BA.  

Now we'll re-run the model with only OBP and SLG as predictors.

```r
runsReg2 <- lm(RS ~ OBP + SLG, data = baseball)
summary(runsReg2)
```

```
## 
## Call:
## lm(formula = RS ~ OBP + SLG, data = baseball)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -77.871 -16.777  -1.116  16.998  93.008 
## 
## Coefficients:
##             Estimate Std. Error t value            Pr(>|t|)    
## (Intercept)  -817.51      16.07  -50.86 <0.0000000000000002 ***
## OBP          2859.24      74.11   38.58 <0.0000000000000002 ***
## SLG          1507.33      34.07   44.24 <0.0000000000000002 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 25.43 on 1319 degrees of freedom
## Multiple R-squared:  0.9211,	Adjusted R-squared:  0.921 
## F-statistic:  7700 on 2 and 1319 DF,  p-value: < 0.00000000000000022
```

The new model is much better. With only two predictors, it is more parsimonious, and it has almost the exact same R^2 value as the previous model.  

Looking at the coefficients for this model, OBP is almost twice as large as SLG. Because they are on the same scale, this tells us that OBP is a better predictor of runs scored than SLG.

**In sum, BA is overvalued, SLG is important, but OBP is more important.**


***
##Building a Model for Runs Allowed  

**Runs allowed (RA)** can be predicted by OOBP (opponent's on base % allowed by the pitchers) and OSLG (opponent's SLG allowed by the pitchers).

Let's build a model to predict RA with OOBP and OSLG.

```r
ramodel <- lm(RA ~ OOBP + OSLG, data = baseball)
summary(ramodel)
```

```
## 
## Call:
## lm(formula = RA ~ OOBP + OSLG, data = baseball)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -84.517 -17.954   0.902  18.424  71.930 
## 
## Coefficients:
##             Estimate Std. Error t value            Pr(>|t|)    
## (Intercept)  -851.48      24.63  -34.57 <0.0000000000000002 ***
## OOBP         2777.06     138.80   20.01 <0.0000000000000002 ***
## OSLG         1639.65      81.13   20.21 <0.0000000000000002 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 26.68 on 507 degrees of freedom
##   (812 observations deleted due to missingness)
## Multiple R-squared:  0.9142,	Adjusted R-squared:  0.9139 
## F-statistic:  2702 on 2 and 507 DF,  p-value: < 0.00000000000000022
```

This model is strong; both OOBP and OSLG are significantly related to RA.  

***
##2016 Predictions

So far we have established a number of key facts in this analysis:

* If a team wins 94 games they will probably make it to the playoffs
* If a team outscores their opponents by 126 runs (RD >= 126), they have a strong chance of meeting the 94-game threshold.
* A team's RD is calcuated by subtracting RA from RS.
    + RS is predicted by OBP and SLG.
    + RA is predicted by OOBP and OSLG.

Given these facts, we will extrapolate from our models to predict OBP, SLG, OOBP, and OSLG for each Chicago team in 2016. Then with those projections, we will predict how many games each Chicago team will win in 2016.  

###Methodology: 2016 Batting Figures

These numbers were available on the internet.  

First, I obtained the projected 2016 roster for both the White Sox and the Cubs.

Then, for each player, I computed composite figures for OBP and SLG, using the results of 4 different sabermetrics projection systems:

 * [Marcel](http://www.tangotiger.net/marcel/)  
 * [Rotochamp](http://rotochamp.com/baseball/Player.aspx?MLBAMID=594809)
 * [Steamer](http://steamerprojections.com/blog/about-2/)
 * [Steamer 600](http://rotochamp.com/baseball/Player.aspx?MLBAMID=594809)  
 
I used multiple systems, so that I could decrease the impact that any one system's error had on the overall prediction.  

Each system is slightly different (you can read more about their own methodologies by clicking the links above), but all of them involve some kind of weighted average of the previous 3 years' statistics.  


###Methodology: 2016 Pitching Figures
I obtained the projected 2016 lineup and bullpen for both the White Sox and the Cubs.

Unfortunately, unlike the batting statistics, the 4 available sabermetrics systems did NOT include projections for OOBS and OSLG.

To make these projections, I simply used a weighted average of the previous 3 years' OOBS and OSLG data for each pitcher in both the lineup and the bullpen, and then weighted *those* figures based on how much playing time each pitcher gets.  

####Weighted Average Based on Past 3 Years
The weights I used were generated in a [previous sabermetrics analysis](http://www.baseballprospectus.com/article.php?articleid=15992). This analysis showed for a given player, how *predictive* were each of his previous season's data for his current season figures. The authors came up with weights of .47, .32, and .18, respectively for each of the previous 3 years.

*Note:* 3 pitchers in my analysis only had 2 seasons worth of data. In these cases, I used weights of 0.6 and 0.4 (roughly equivalent to the ratio of (.47/.32)). For players with only 1 year's worth of data, I weighted that data at 1. I excluded all rookie players from the analysis.

####Weighted Average Based on Playing Time
After generating each pitcher's weighted OOBP and OSLG, I then weighted each of those figures based on how much playing time a pitcher gets (to do this, for each pitcher I computed the proportion of batters they faced to the team's sum total of batters faced in the 2015 season). My rationale was that the pitchers who face more batters, such as starting pitchers, should be weighted more heavily.

***
***
##2016 Predicted Figures

Using these methodologies, I obtained the following figures:

Team Statistic | Projected Figure
------------- | -------------
White Sox OBP | 0.321666667 
White Sox SLG | 0.41475
Cubs OBP | 0.34253125
Cubs SLG | 0.43878125
White Sox OOBP | 0.320957143
White Sox OSLG | 0.38205
Cubs OOBP | 0.29843875
Cubs OSLG | 0.353745

**It appears that in 2016 - in both batting and pitching - the Chicago Cubs will outperform the Chicago White Sox.**


***
###Predicting Outcomes for the Chicago White Sox

But what does this mean for the White Sox in terms of making the playoffs?  

Remember: if we can predict the runs scored and runs allowed for a team, we can also predict how many games they will win.

Using the runs scored linear regression model (the one that uses OBP and SLG as independent variables), we can find the number of runs we expect the White Sox to score:


```r
summary(runsReg2)
```

```
## 
## Call:
## lm(formula = RS ~ OBP + SLG, data = baseball)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -77.871 -16.777  -1.116  16.998  93.008 
## 
## Coefficients:
##             Estimate Std. Error t value            Pr(>|t|)    
## (Intercept)  -817.51      16.07  -50.86 <0.0000000000000002 ***
## OBP          2859.24      74.11   38.58 <0.0000000000000002 ***
## SLG          1507.33      34.07   44.24 <0.0000000000000002 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 25.43 on 1319 degrees of freedom
## Multiple R-squared:  0.9211,	Adjusted R-squared:  0.921 
## F-statistic:  7700 on 2 and 1319 DF,  p-value: < 0.00000000000000022
```

```r
obp_chw_2016 <- 0.321666667 
slg_chw_2016 <- 0.41475
runspredicted_chw_2016 <- -817.51 + 2859.24*obp_chw_2016 + 1507.33*slg_chw_2016
runspredicted_chw_2016
```

```
## [1] 727.3773
```
The White Sox are predicted to score ~727 runs in 2016.

Now we can also find the predicted runs allowed, using the runs allowed regression. Remember, this uses opponents OBP (OOBP) and oppenents SLG (OSLG). 


```r
summary(ramodel)
```

```
## 
## Call:
## lm(formula = RA ~ OOBP + OSLG, data = baseball)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -84.517 -17.954   0.902  18.424  71.930 
## 
## Coefficients:
##             Estimate Std. Error t value            Pr(>|t|)    
## (Intercept)  -851.48      24.63  -34.57 <0.0000000000000002 ***
## OOBP         2777.06     138.80   20.01 <0.0000000000000002 ***
## OSLG         1639.65      81.13   20.21 <0.0000000000000002 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 26.68 on 507 degrees of freedom
##   (812 observations deleted due to missingness)
## Multiple R-squared:  0.9142,	Adjusted R-squared:  0.9139 
## F-statistic:  2702 on 2 and 507 DF,  p-value: < 0.00000000000000022
```

```r
oobp_chw_2016 <- 0.320957143
oslg_chw_2016 <- 0.38205
runsAllowedPredicted_chw_2016 <- -851.48 + 2777.06*oobp_chw_2016 + 1639.65*oslg_chw_2016
runsAllowedPredicted_chw_2016
```

```
## [1] 666.2655
```
The White Sox are predicted to allow their opponents to score ~666 runs 2016.


Now we can calculate the predicted difference between runs scored and runs allowed.

```r
predictedRD_chw <- runspredicted_chw_2016-runsAllowedPredicted_chw_2016
predictedRD_chw
```

```
## [1] 61.11179
```

How many games will White Sox win? We can use the runs differential regression to predict 2016 wins.

```r
wins_chw <- 80.910741 + 0.104284*predictedRD_chw
wins_chw
```

```
## [1] 87.28372
```

**Thus, the Chicago White Sox are predicted to win 87 games (<94), meaning they will probably NOT make the playoffs in 2016.**


***
###Predicting Outcomes for the Chicago Cubs
Using the same runs scored linear regression model, we can find the number of runs we expect the Cubs to score:


```r
summary(runsReg2)
```

```
## 
## Call:
## lm(formula = RS ~ OBP + SLG, data = baseball)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -77.871 -16.777  -1.116  16.998  93.008 
## 
## Coefficients:
##             Estimate Std. Error t value            Pr(>|t|)    
## (Intercept)  -817.51      16.07  -50.86 <0.0000000000000002 ***
## OBP          2859.24      74.11   38.58 <0.0000000000000002 ***
## SLG          1507.33      34.07   44.24 <0.0000000000000002 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 25.43 on 1319 degrees of freedom
## Multiple R-squared:  0.9211,	Adjusted R-squared:  0.921 
## F-statistic:  7700 on 2 and 1319 DF,  p-value: < 0.00000000000000022
```

```r
obp_chc_2016 <- 0.34253125
slg_chc_2016 <- 0.43878125
runspredicted_chc_2016 <- -817.51 + 2859.24*obp_chc_2016 + 1507.33*slg_chc_2016
runspredicted_chc_2016
```

```
## [1] 823.2572
```
The Cubs are predicted to score ~823 runs in 2016.  


Now we will use the runs allowed regression again to predict how many runs the Cubs will allow.

```r
summary(ramodel)
```

```
## 
## Call:
## lm(formula = RA ~ OOBP + OSLG, data = baseball)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -84.517 -17.954   0.902  18.424  71.930 
## 
## Coefficients:
##             Estimate Std. Error t value            Pr(>|t|)    
## (Intercept)  -851.48      24.63  -34.57 <0.0000000000000002 ***
## OOBP         2777.06     138.80   20.01 <0.0000000000000002 ***
## OSLG         1639.65      81.13   20.21 <0.0000000000000002 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 26.68 on 507 degrees of freedom
##   (812 observations deleted due to missingness)
## Multiple R-squared:  0.9142,	Adjusted R-squared:  0.9139 
## F-statistic:  2702 on 2 and 507 DF,  p-value: < 0.00000000000000022
```

```r
oobp_chc_2016 <- 0.29843875
oslg_chc_2016 <- 0.353745
runsAllowedPredicted_chc_2016 <- -851.48 + 2777.06*oobp_chc_2016 + 1639.65*oslg_chc_2016
runsAllowedPredicted_chc_2016
```

```
## [1] 557.3203
```
The Cubs are predicted to limit their opponents to 557 runs.

Now we will calculate the predicted difference between runs scored and runs allowed for the Cubs.

```r
predictedRD_chc <- runspredicted_chc_2016-runsAllowedPredicted_chc_2016
predictedRD_chc
```

```
## [1] 265.9369
```

And with an RD ~266, how many games will the Cubs actually win?

```r
wins_chc <- 80.910741 + 0.104284*predictedRD_chc
wins_chc
```

```
## [1] 108.6437
```

**The Chicago Cubs are predicted to win 108 games (>94), meaning they WILL probably make the playoffs in 2016.**

This is a truly outstanding runs differential. To put it in perspective, remember this graph:

![plot of chunk unnamed-chunk-18](figure/unnamed-chunk-18-1.png) 


This graph contains historical data for 50+ years of baseball. A runs differential of 266 would position the 2016 Cubs as one of the highest performing baseball teams of all time. Could be a great year for Cubs fans!

***

###Summary

* The Chicago White Sox are projected to win 87 games in 2016 and NOT make the playoffs.  
* The Chicago Cubs are projected to win 108 games in 2016 and TO make the playoffs. Moreover, there is a good chance the 2016 Cubs will have an outstanding season!  

See more on this report (and more) at my blog: https://kurtschuepfer.wordpress.com/
