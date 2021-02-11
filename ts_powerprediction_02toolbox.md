The Prediction of Power Consumption in Korea
================

### Time Series Analysis

### 2\. The Forecaster’s Toolbox

  - Training Data: 2003/01 - 2013/13; 총 11년치
  - Testing Data: 2014/01 - 2017/11; 총 4년치(3년 11개월)

<!-- end list -->

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
y0<-window(pc, start=c(2003,01), end=c(2013,12)) #training data
y1<-window(pc, start=c(2014,01), end=c(2017,11)) #test data
```

#### 2.1.1 Fitting models on Training Data

``` r
m<-meanf(y0, h=47) # average method
n<-naive(y0, h=47) # naive method; recent value
r<-rwf(y0, h=47) # random walk without drift
rd<-rwf(y0, h=47, drift=T) # random walk with drift
s<-snaive(y0, h=47) # seasonal naive method
#head(m)
#head(n)
#head(r)
#head(rd)
#head(s)
```

#### 2.1.2 Prediction Accuracy; test 데이터의 실제값과 예측값 비교

``` r
accuracy(m,y1)
```

    ##                        ME      RMSE       MAE       MPE     MAPE     MASE
    ## Training set 4.038302e-13  840.3746  716.5621 -2.569314 13.91291 2.844337
    ## Test set     1.348631e+03 1424.5973 1348.6313 19.893772 19.89377 5.353287
    ##                   ACF1 Theil's U
    ## Training set 0.9082023        NA
    ## Test set     0.5220907  3.110407

``` r
accuracy(n,y1)
```

    ##                      ME     RMSE      MAE        MPE     MAPE     MASE
    ## Training set   19.85496 321.6906 266.0748  0.1932667 4.907173 1.056163
    ## Test set     -248.57021 521.9753 445.1021 -4.2232872 6.895799 1.766798
    ##                   ACF1 Theil's U
    ## Training set 0.2335832        NA
    ## Test set     0.5220907  1.210122

``` r
accuracy(r,y1)
```

    ##                      ME     RMSE      MAE        MPE     MAPE     MASE
    ## Training set   19.85496 321.6906 266.0748  0.1932667 4.907173 1.056163
    ## Test set     -248.57021 521.9753 445.1021 -4.2232872 6.895799 1.766798
    ##                   ACF1 Theil's U
    ## Training set 0.2335832        NA
    ## Test set     0.5220907  1.210122

``` r
accuracy(rd,y1)
```

    ##                         ME     RMSE      MAE         MPE      MAPE
    ## Training set -6.210249e-12 321.0773 265.6966  -0.1900106  4.911311
    ## Test set     -7.250893e+02 862.3233 731.7993 -11.3468830 11.439635
    ##                  MASE      ACF1 Theil's U
    ## Training set 1.054662 0.2335832        NA
    ## Test set     2.904820 0.4966049  1.995434

``` r
accuracy(s,y1)
```

    ##                    ME     RMSE      MAE      MPE     MAPE     MASE
    ## Training set 236.1825 300.7939 251.9258 4.421256 4.687770 1.000000
    ## Test set     250.4085 345.4460 275.2043 3.658414 4.039532 1.092402
    ##                   ACF1 Theil's U
    ## Training set 0.3801801        NA
    ## Test set     0.4263694 0.7477496

MAPE(Mean Absolute Percentage Error) 기준 =\> snaive 방법의 MAPE가 가장 낮아 가장 좋은
모델이라고 할 수 있다.

| Model                  | MAPE  |
| ---------------------- | ----- |
| meanf                  | 19.89 |
| naive                  | 6.89  |
| random walk            | 6.89  |
| random walk with drift | 11.43 |
| snaive                 | 4.04  |

#### 2.1.3 Plotting

``` r
autoplot(pc)+autolayer(m, PI=F, series='meanf', size=2)+autolayer(n, PI=F, series='naive', size=2)+autolayer(rd, PI=F, series='rwf(drift=T)', size=2)+autolayer(s, PI=F, series='snaive', size=2)+ geom_vline(xintercept=2014, linetype=2) + geom_point(aes(x=x, y=y))
```

![](ts_powerprediction_02toolbox_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

  - snaive가 가장 잘 맞는다.

#### 2.2 Residual Diagnotics

``` r
res<-residuals(snaive(pc))
autoplot(res)+xlab('Month')+ylab('MW')+ggtitle('Residuals from snaive method')
```

![](ts_powerprediction_02toolbox_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
# histogram
gghistogram(res)+ggtitle('Histogram of residuals')
```

    ## Warning: `list_len()` is deprecated as of rlang 0.2.0.
    ## Please use `new_list()` instead.
    ## This warning is displayed once per session.

    ## Warning: Removed 12 rows containing non-finite values (stat_bin).

![](ts_powerprediction_02toolbox_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

``` r
# Acf of residuals
ggAcf(res)+ggtitle('ACF of residuals')
```

![](ts_powerprediction_02toolbox_files/figure-gfm/unnamed-chunk-5-3.png)<!-- -->

  - The bigger lag, the less ACF 시차가 커질수록 자기상관이 줄어듦을 확인할 수 있다.

#### 2.2.1 Pormanteau Test for Autocorrelation 자기상관 검정

  
![ H\_{0}: ](https://latex.codecogs.com/png.latex?%20H_%7B0%7D%3A%20
" H_{0}: ")  
& White noise exists

``` r
Box.test(res, lag=10, fitdf=0, type='Lj')
```

    ## 
    ##  Box-Ljung test
    ## 
    ## data:  res
    ## X-squared = 102.8, df = 10, p-value < 2.2e-16

``` r
checkresiduals(snaive(pc))
```

![](ts_powerprediction_02toolbox_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

    ## 
    ##  Ljung-Box test
    ## 
    ## data:  Residuals from Seasonal naive method
    ## Q* = 123.89, df = 24, p-value = 1.998e-15
    ## 
    ## Model df: 0.   Total lags used: 24

#### 2.3 Prediction Intervals

``` r
autoplot(snaive(pc))
```

![](ts_powerprediction_02toolbox_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->
