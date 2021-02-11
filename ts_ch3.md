Chapter3 The forecaster’s toolbox
================

## 3.1 Some simple forecasting methods

### Average method

  - ??????: ???տ? ?ڷ??? ???? \* ?߼??? ??��?? ???? ?Ұ?, ?????̿??? ??

<!-- end list -->

``` r
#install.packages('fpp2')
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
y<-ts(c(1,2,3), start=2015)
meanf(y,3) #meanf=meanforecast
```

    ##      Point Forecast      Lo 80    Hi 80     Lo 95    Hi 95
    ## 2018              2 -0.1773242 4.177324 -2.968275 6.968275
    ## 2019              2 -0.1773242 4.177324 -2.968275 6.968275
    ## 2020              2 -0.1773242 4.177324 -2.968275 6.968275

### Naive method, Random Walk

  - ??????: ???? ?ֱ??? ??????

<!-- end list -->

``` r
naive(y, 3)
```

    ##      Point Forecast    Lo 80    Hi 80      Lo 95    Hi 95
    ## 2018              3 1.718448 4.281552  1.0400355 4.959964
    ## 2019              3 1.187612 4.812388  0.2281917 5.771808
    ## 2020              3 0.780287 5.219713 -0.3947581 6.394758

``` r
rwf(y, 3) # Alternative #random walk forecast
```

    ##      Point Forecast    Lo 80    Hi 80      Lo 95    Hi 95
    ## 2018              3 1.718448 4.281552  1.0400355 4.959964
    ## 2019              3 1.187612 4.812388  0.2281917 5.771808
    ## 2020              3 0.780287 5.219713 -0.3947581 6.394758

### Seasonal naive method

  - ??????: (?ֱ? ?ٰ?)???? ?ֱ??? ???? ??????

<!-- end list -->

``` r
snaive(y, 3)
```

    ##      Point Forecast    Lo 80    Hi 80      Lo 95    Hi 95
    ## 2018              3 1.718448 4.281552  1.0400355 4.959964
    ## 2019              3 1.187612 4.812388  0.2281917 5.771808
    ## 2020              3 0.780287 5.219713 -0.3947581 6.394758

### Drift method, Random Walk with drift

``` r
#rwf(y, 3, drift=TRUE)
```

### Examples

``` r
# Set training data from 1992-2007
beer2<-window(ausbeer, start=1992, end=c(2007,4))
# Plot some forecasts
autoplot(beer2) + 
  forecast::autolayer(meanf(beer2, h=11), PI=FALSE, series="Mean") +
  forecast::autolayer(naive(beer2, h=11), PI=FALSE, series="Naive") +
  forecast::autolayer(snaive(beer2, h=11), PI=FALSE, series="Seasonal naive") +
  ggtitle("Forecasts for quarterly beer production") +
  xlab("Year") + ylab("Megaliters") +
  guides(colour=guide_legend(title="Forecast"))
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
#Set training data to first 250 days
dj2<-window(dj, end=250)
# Plot some forecasts
autoplot(dj2) +
  forecast::autolayer(meanf(dj2, h=42), PI=FALSE, series="Mean") +
  forecast::autolayer(rwf(dj2, h=42), PI=FALSE, series="Naïve") +
  forecast::autolayer(rwf(dj2, drift=TRUE, h=42), PI=FALSE, series="Drift") +
  ggtitle("Dow Jones Index (daily ending 15 Jul 94)") +
  xlab("Day") + ylab("") +
  guides(colour=guide_legend(title="Forecast"))
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-5-2.png)<!-- --> \#\# 3.2
Transformations and adjustments \#\#\# Calender adjustments

``` r
cbind(Monthly = milk, DailyAverage=milk/monthdays(milk)) %>%
  autoplot(facet=TRUE) + xlab("Years") + ylab("Pounds") +
    ggtitle("Milk production per cow")
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

### Population adjustments

### Inflation adjustments

### Mathematical transformations

``` r
(lambda <- BoxCox.lambda(elec))
```

    ## [1] 0.2654076

``` r
#> [1] 0.265
autoplot(BoxCox(elec,lambda))
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

### Features of power transformations

### Bias adjustments

``` r
fc <- rwf(eggs, drift=TRUE, lambda=0, h=50, level=80)
fc2 <- rwf(eggs, drift=TRUE, lambda=0, h=50, level=80, biasadj=TRUE)
autoplot(eggs) +
  forecast::autolayer(fc, series="Simple back transformation") +
  forecast::autolayer(fc2, PI=FALSE, series="Bias adjusted") +
  guides(colour=guide_legend(title="Forecast"))
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## 3.3 Residual diagnostics

### Fitted value

### Residuals

### Example: Forecasting the Dow-Jones Index

``` r
dj2 <- window(dj, end=250)
autoplot(dj2) + xlab("Day") + ylab("") +
  ggtitle("Dow Jones Index (daily ending 15 Jul 94)")
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
res <- residuals(naive(dj2))
autoplot(res) + xlab("Day") + ylab("") +
  ggtitle("Residuals from naïve method")
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->

``` r
gghistogram(res) + ggtitle("Histogram of residuals")
```

    ## Warning: `list_len()` is deprecated as of rlang 0.2.0.
    ## Please use `new_list()` instead.
    ## This warning is displayed once per session.

    ## Warning: Removed 1 rows containing non-finite values (stat_bin).

![](ts_ch3_files/figure-gfm/unnamed-chunk-9-3.png)<!-- -->

``` r
ggAcf(res) + ggtitle("ACF of residuals")
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-9-4.png)<!-- -->

### Pormanteau tests for autocorrelation

``` r
# lag=h and fitdf=K
Box.test(res, lag=10, fitdf=0)
```

    ## 
    ##  Box-Pierce test
    ## 
    ## data:  res
    ## X-squared = 10.655, df = 10, p-value = 0.385

``` r
#> 
#>  Box-Pierce test
#> 
#> data:  res
#> X-squared = 10, df = 10, p-value = 0.4

Box.test(res,lag=10, fitdf=0, type="Lj")
```

    ## 
    ##  Box-Ljung test
    ## 
    ## data:  res
    ## X-squared = 11.088, df = 10, p-value = 0.3507

``` r
#> 
#>  Box-Ljung test
#> 
#> data:  res
#> X-squared = 10, df = 10, p-value = 0.4
checkresiduals(naive(dj2))
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

    ## 
    ##  Ljung-Box test
    ## 
    ## data:  Residuals from Naive method
    ## Q* = 11.088, df = 10, p-value = 0.3507
    ## 
    ## Model df: 0.   Total lags used: 10

``` r
#> 
#>  Ljung-Box test
#> 
#> data:  Residuals from Naive method
#> Q* = 10, df = 10, p-value = 0.4
#> 
#> Model df: 0.   Total lags used: 10
```

## 3.4 Evaluating forecast accuract

### Training and test sets

### Forecast errors

### Scale-dependent errors

### Percentage errors

### Scales errors

### Examples

``` r
beer2 <- window(ausbeer,start=1992,end=c(2007,4))
beerfit1 <- meanf(beer2,h=10)
beerfit2 <- rwf(beer2,h=10)
beerfit3 <- snaive(beer2,h=10)
autoplot(window(ausbeer, start=1992)) +
  forecast::autolayer(beerfit1, series="Mean", PI=FALSE) +
  forecast::autolayer(beerfit2, series="Naïve", PI=FALSE) +
  forecast::autolayer(beerfit3, series="Seasonal naïve", PI=FALSE) +
  xlab("Year") + ylab("Megalitres") +
  ggtitle("Forecasts for quarterly beer production") +
  guides(colour=guide_legend(title="Forecast"))
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

``` r
beer3 <- window(ausbeer, start=2008)
accuracy(beerfit1, beer3)
```

    ##                   ME     RMSE      MAE        MPE     MAPE     MASE
    ## Training set   0.000 43.62858 35.23438 -0.9365102 7.886776 2.463942
    ## Test set     -13.775 38.44724 34.82500 -3.9698659 8.283390 2.435315
    ##                     ACF1 Theil's U
    ## Training set -0.10915105        NA
    ## Test set     -0.06905715  0.801254

``` r
accuracy(beerfit2, beer3)
```

    ##                       ME     RMSE      MAE         MPE     MAPE     MASE
    ## Training set   0.4761905 65.31511 54.73016  -0.9162496 12.16415 3.827284
    ## Test set     -51.4000000 62.69290 57.40000 -12.9549160 14.18442 4.013986
    ##                     ACF1 Theil's U
    ## Training set -0.24098292        NA
    ## Test set     -0.06905715  1.254009

``` r
accuracy(beerfit3, beer3)
```

    ##                     ME     RMSE  MAE        MPE     MAPE      MASE
    ## Training set -2.133333 16.78193 14.3 -0.5537713 3.313685 1.0000000
    ## Test set      5.200000 14.31084 13.4  1.1475536 3.168503 0.9370629
    ##                    ACF1 Theil's U
    ## Training set -0.2876333        NA
    ## Test set      0.1318407  0.298728

``` r
dj2 <- window(dj, end=250)
djfc1 <- meanf(dj2, h=42)
djfc2 <- rwf(dj2, h=42)
djfc3 <- rwf(dj2, drift=TRUE, h=42)
autoplot(dj) +
  forecast::autolayer(djfc1, PI=FALSE, series="Mean") +
  forecast::autolayer(djfc2, PI=FALSE, series="Naïve") +
  forecast::autolayer(djfc3, PI=FALSE, series="Drift") +
  xlab("Day") + ylab("") +
  ggtitle("Dow Jones Index (daily ending 15 Jul 94)") +
  guides(colour=guide_legend(title="Forecast"))
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-11-2.png)<!-- -->

``` r
dj3 <- window(dj, start=251)
accuracy(djfc1, dj3)
```

    ##                        ME      RMSE       MAE         MPE     MAPE
    ## Training set 6.553874e-14  98.71439  80.56688 -0.06934572 2.151962
    ## Test set     1.424185e+02 148.23574 142.41848  3.66304611 3.663046
    ##                  MASE      ACF1 Theil's U
    ## Training set 4.920567 0.9719593        NA
    ## Test set     8.698111 0.8255136  6.072223

``` r
accuracy(djfc2, dj3)
```

    ##                      ME     RMSE      MAE        MPE      MAPE     MASE
    ## Training set  0.7188755 22.00014 16.37349 0.01749683 0.4380973 1.000000
    ## Test set     46.4404762 62.02846 54.44048 1.18683463 1.3979371 3.324915
    ##                    ACF1 Theil's U
    ## Training set 0.02446257        NA
    ## Test set     0.82551365   2.54582

``` r
accuracy(djfc3, dj3)
```

    ##                         ME     RMSE      MAE          MPE      MAPE
    ## Training set -6.317152e-12 21.98839 16.34525 -0.001766862 0.4373707
    ## Test set      3.098465e+01 53.69767 45.72743  0.787547945 1.1757748
    ##                   MASE       ACF1 Theil's U
    ## Training set 0.9982752 0.02446257        NA
    ## Test set     2.7927719 0.83881869  2.203742

### Time series cross-validation

``` r
e <- tsCV(dj, rwf, drift=TRUE, h=1)
sqrt(mean(e^2, na.rm=TRUE))
```

    ## [1] 22.68249

``` r
#> [1] 22.7
sqrt(mean(residuals(rwf(dj, drift=TRUE))^2, na.rm=TRUE))
```

    ## [1] 22.49681

``` r
#> [1] 22.5

dj %>% tsCV(forecastfunction=rwf, drift=TRUE, h=1) -> e
e^2 %>% mean(na.rm=TRUE) %>% sqrt
```

    ## [1] 22.68249

``` r
dj %>% rwf(drift=TRUE) %>% residuals -> res
res^2 %>% mean(na.rm=TRUE) %>% sqrt
```

    ## [1] 22.49681

## 3.5 Prediction intervals

``` r
naive(dj2)
```

    ##     Point Forecast    Lo 80    Hi 80    Lo 95    Hi 95
    ## 251           3830 3801.804 3858.196 3786.878 3873.122
    ## 252           3830 3790.125 3869.875 3769.016 3890.984
    ## 253           3830 3781.163 3878.837 3755.311 3904.689
    ## 254           3830 3773.608 3886.392 3743.756 3916.244
    ## 255           3830 3766.952 3893.048 3733.577 3926.423
    ## 256           3830 3760.935 3899.065 3724.373 3935.627
    ## 257           3830 3755.401 3904.599 3715.910 3944.090
    ## 258           3830 3750.250 3909.750 3708.033 3951.967
    ## 259           3830 3745.412 3914.588 3700.634 3959.366
    ## 260           3830 3740.837 3919.163 3693.637 3966.363

``` r
#>     Point Forecast Lo 80 Hi 80 Lo 95 Hi 95
#> 251           3830  3802  3858  3787  3873
#> 252           3830  3790  3870  3769  3891
#> 253           3830  3781  3879  3755  3905
#> 254           3830  3774  3886  3744  3916
#> 255           3830  3767  3893  3734  3926
#> 256           3830  3761  3899  3724  3936
#> 257           3830  3755  3905  3716  3944
#> 258           3830  3750  3910  3708  3952
#> 259           3830  3745  3915  3701  3959
#> 260           3830  3741  3919  3694  3966
autoplot(naive(dj2))
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

## 3.6 The forecast package

### Functions that output a forecast object:

*meanf() *naive(), snaive() *rwf() *croston() *stlf() *ses() *holt(),
hw() *splinef() *thetaf() *forecast()

### forecast() function

``` r
forecast(ausbeer)
```

    ##         Point Forecast    Lo 80    Hi 80    Lo 95    Hi 95
    ## 2010 Q3       404.6025 385.8695 423.3355 375.9529 433.2521
    ## 2010 Q4       480.3982 457.5283 503.2682 445.4216 515.3748
    ## 2011 Q1       417.0367 396.5112 437.5622 385.6456 448.4277
    ## 2011 Q2       383.0996 363.5063 402.6930 353.1341 413.0651
    ## 2011 Q3       403.0006 380.1438 425.8574 368.0442 437.9570
    ## 2011 Q4       478.4943 450.1973 506.7913 435.2177 521.7708
    ## 2012 Q1       415.3821 389.6837 441.0806 376.0797 454.6845
    ## 2012 Q2       381.5781 356.8145 406.3418 343.7053 419.4509

``` r
#>         Point Forecast Lo 80 Hi 80 Lo 95 Hi 95
#> 2010 Q3            405   386   423   376   433
#> 2011 Q4            480   458   503   446   515
#> 2011 Q1            417   397   437   386   448
#> 2011 Q2            383   364   402   354   412
#> 2012 Q3            403   381   425   369   437
#> 2012 Q4            478   451   506   436   521
#> 2012 Q1            415   390   441   377   454
#> 2012 Q2            382   357   406   345   419
```

## 3.7 Exercises

``` r
#install.packages('fpp2')
library(fpp2)
y0<-c(4,10,18,7,1,3,10,2)
y1<-c(3,5,15,5)
y<-ts(c(y0,y1), start=c(2014,1), freq=4) # 3??ġ ?ð迭 ??ü ????
# window?Լ?: ts??ü?κ??? ?κ????? ?̾Ƴ? ?? ????
# 8:2 = training set:test set
y0<-window(y, start=c(2014,1), end=c(2015,4)) # training set ?Ʒÿ? ??????
y1<-window(y, start=c(2016,1), end=c(2016,4)) # test set ?????? ??????

#S1: Fitting models on trn
m<-meanf(y0, h=4) # ??�� ?? ?б? ??????�� '???չ?'��?? ???ϱ?
n<-naive(y0, h=4)
r<-rwf(y0, h=4)
rd<-rwf(y0, h=4)
s<-snaive(y0, h=4)
str(m)
```

    ## List of 11
    ##  $ method   : chr "Mean"
    ##  $ level    : num [1:2] 80 95
    ##  $ x        : Time-Series [1:8] from 2014 to 2016: 4 10 18 7 1 3 10 2
    ##  $ series   : chr "y0"
    ##  $ mean     : Time-Series [1:4] from 2016 to 2017: 6.88 6.88 6.88 6.88
    ##  $ lower    : Time-Series [1:4, 1:2] from 2016 to 2017: -1.63 -1.63 -1.63 -1.63 -7.34 ...
    ##   ..- attr(*, "dimnames")=List of 2
    ##   .. ..$ : NULL
    ##   .. ..$ : chr [1:2] "80%" "95%"
    ##  $ upper    : Time-Series [1:4, 1:2] from 2016 to 2017: 15.4 15.4 15.4 15.4 21.1 ...
    ##   ..- attr(*, "dimnames")=List of 2
    ##   .. ..$ : NULL
    ##   .. ..$ : chr [1:2] "80%" "95%"
    ##  $ model    :List of 5
    ##   ..$ mu       : num 6.88
    ##   ..$ mu.se    : num 2
    ##   ..$ sd       : num 5.67
    ##   ..$ bootstrap: logi FALSE
    ##   ..$ call     : language meanf(y = y0, h = 4)
    ##  $ lambda   : NULL
    ##  $ fitted   : Time-Series [1:8] from 2014 to 2016: 6.88 6.88 6.88 6.88 6.88 ...
    ##  $ residuals: Time-Series [1:8] from 2014 to 2016: -2.875 3.125 11.125 0.125 -5.875 ...
    ##  - attr(*, "class")= chr "forecast"

``` r
#m?? ???????? ??�� ?????? y1???? ????(?? ???? ??)
y1-m$mean
```

    ##        Qtr1   Qtr2   Qtr3   Qtr4
    ## 2016 -3.875 -1.875  8.125 -1.875

``` r
#s?? ???????? ??�� ?????? y1???? ????(?? ???? ??)
y1-s$mean
```

    ##      Qtr1 Qtr2 Qtr3 Qtr4
    ## 2016    2    2    5    3

``` r
#S2: Prediction accuracy ??�� ?????? ?????? ?????? ?? ?????? ???? ???跮
# test set ????��?? ?��???

accuracy(m, y1)
```

    ##                 ME     RMSE    MAE      MPE      MAPE      MASE
    ## Training set 0.000 5.301828 4.3750 -113.275 144.79787 0.7608696
    ## Test set     0.125 4.692081 3.9375  -37.500  64.58333 0.6847826
    ##                     ACF1 Theil's U
    ## Training set  0.09720678        NA
    ## Test set     -0.27272727 0.7894456

``` r
accuracy(n, y1)
```

    ##                      ME     RMSE      MAE       MPE     MAPE      MASE
    ## Training set -0.2857143 7.309485 6.857143 -130.8617 199.7506 1.1925466
    ## Test set      5.0000000 6.855655 5.000000   60.0000  60.0000 0.8695652
    ##                     ACF1 Theil's U
    ## Training set -0.07519948        NA
    ## Test set     -0.27272727  1.263113

``` r
accuracy(r, y1)
```

    ##                      ME     RMSE      MAE       MPE     MAPE      MASE
    ## Training set -0.2857143 7.309485 6.857143 -130.8617 199.7506 1.1925466
    ## Test set      5.0000000 6.855655 5.000000   60.0000  60.0000 0.8695652
    ##                     ACF1 Theil's U
    ## Training set -0.07519948        NA
    ## Test set     -0.27272727  1.263113

``` r
accuracy(rd, y1)
```

    ##                      ME     RMSE      MAE       MPE     MAPE      MASE
    ## Training set -0.2857143 7.309485 6.857143 -130.8617 199.7506 1.1925466
    ## Test set      5.0000000 6.855655 5.000000   60.0000  60.0000 0.8695652
    ##                     ACF1 Theil's U
    ## Training set -0.07519948        NA
    ## Test set     -0.27272727  1.263113

``` r
accuracy(s, y1)
```

    ##                 ME     RMSE  MAE       MPE     MAPE      MASE       ACF1
    ## Training set -5.75 6.062178 5.75 -215.8333 215.8333 1.0000000 -0.1567797
    ## Test set      3.00 3.240370 3.00   50.0000  50.0000 0.5217391 -0.1666667
    ##              Theil's U
    ## Training set        NA
    ## Test set     0.5510321

``` r
#S3: Plotting
#autolayer ???ļ? ?׷��?
autoplot(y) +
  autolayer(m, PI=F, series='meanf', size=2) +
  autolayer(n, PI=F, series='naive', size=2) +
  autolayer(rd, PI=F, series='rwf(drift=T)', size=2) +
  autolayer(s, PI=F, series='snaive', size=2) +
  geom_vline(xintercept=2016, linetype=2) +
  geom_point(aes(x=x, y=y))
```

![](ts_ch3_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->
