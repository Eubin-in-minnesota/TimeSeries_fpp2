The Prediction of Power Consumption in Korea
================

### Time Series Analysis

### 3\. Linear Regression Models

``` r
library(fpp2, quietly=T)
```

    ## Warning: As of rlang 0.4.0, dplyr must be at least version 0.8.0.
    ## * dplyr 0.7.8 is too old for rlang 0.4.5.
    ## * Please update dplyr to the latest version.
    ## * Updating packages on Windows requires precautions:
    ##   <https://github.com/jennybc/what-they-forgot/issues/62>

``` r
powerc<-read.csv('C:/timeseries/powerExampleData.csv')
pc<-ts(powerc, start=2003, frequency = 12)
```

#### 3.1 trend(선형추세) + season(분기별 계절 가변수) 자동 생성

``` r
fit.pc<-tslm(pc ~ trend + season)
summary(fit.pc)
```

    ## 
    ## Call:
    ## tslm(formula = pc ~ trend + season)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -591.25 -147.90   22.09  149.13  661.46 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 4583.830     64.159  71.445  < 2e-16 ***
    ## trend         17.229      0.325  53.016  < 2e-16 ***
    ## season2     -178.889     81.895  -2.184  0.03034 *  
    ## season3     -434.085     81.897  -5.300 3.64e-07 ***
    ## season4     -820.694     81.900 -10.021  < 2e-16 ***
    ## season5     -954.456     81.904 -11.653  < 2e-16 ***
    ## season6     -600.225     81.910  -7.328 9.71e-12 ***
    ## season7     -236.941     81.917  -2.892  0.00434 ** 
    ## season8     -194.756     81.925  -2.377  0.01858 *  
    ## season9     -723.212     81.935  -8.827 1.45e-15 ***
    ## season10    -896.234     81.946 -10.937  < 2e-16 ***
    ## season11    -571.957     81.958  -6.979 6.79e-11 ***
    ## season12     -62.610     83.359  -0.751  0.45367    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 224.3 on 166 degrees of freedom
    ## Multiple R-squared:  0.9497, Adjusted R-squared:  0.9461 
    ## F-statistic: 261.5 on 12 and 166 DF,  p-value: < 2.2e-16

``` r
autoplot(pc, series="Data")+forecast::autolayer(fitted(fit.pc), series="Fitted")+xlab('Year')+ylab('MW')+ggtitle('Average of Monthly Power Consumption in Korea')
```

![](ts_powerprediction_03linearregression_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

#### 3.2 Evaluating the Regression Model

``` r
checkresiduals(fit.pc)
```

![](ts_powerprediction_03linearregression_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

    ## 
    ##  Breusch-Godfrey test for serial correlation of order up to 24
    ## 
    ## data:  Residuals from Linear regression model
    ## LM test = 120.81, df = 24, p-value = 6.964e-15

``` r
fit<-tslm(pc~trend+season)
cbind(Fitted=fitted(fit), Residuals=residuals(fit)) %>%
  as.data.frame %>%
  ggplot(aes(x=Fitted, y=Residuals)) + geom_point()
```

![](ts_powerprediction_03linearregression_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

  - 분산이 고르게 퍼져 있다.(이분산성)
