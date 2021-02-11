The Prediction of Power Consumption in Korea
================

### Forecasting with ARIMA

``` r
library(fpp2)
```

    ## Loading required package: ggplot2

    ## Warning: As of rlang 0.4.0, dplyr must be at least version 0.8.0.
    ## * dplyr 0.7.8 is too old for rlang 0.4.5.
    ## * Please update dplyr to the latest version.
    ## * Updating packages on Windows requires precautions:
    ##   <https://github.com/jennybc/what-they-forgot/issues/62>

    ## Loading required package: forecast

    ## Loading required package: fma

    ## Loading required package: expsmooth

``` r
powerc<-read.csv('C:/timeseries/powerExampleData.csv')
pc<-ts(powerc, start=2003, frequency = 12)
apc<-auto.arima(pc, seasonal=T, stepwise=F, approx=T)
```

``` r
farima<-forecast(apc, h=35)
autoplot(farima)
```

![](ts_powerprediction_05arima_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
checkresiduals(farima)
```

![](ts_powerprediction_05arima_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

    ## 
    ##  Ljung-Box test
    ## 
    ## data:  Residuals from ARIMA(1,1,1)(0,1,1)[12]
    ## Q* = 62.078, df = 21, p-value = 6.159e-06
    ## 
    ## Model df: 3.   Total lags used: 24
