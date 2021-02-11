Ch06 Time Series Decomposition
================

  - Classical decomposition: decompose(x,
    type=’addictive|multiplicative) \* decomposed.ts 객체반환 \* seasonal
    계절 성분 \* trend 추세 성분 \* random 랜덤 성분
  - STL: stl(x, s.window, t.window) \* stl 객체 반환

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
data(hsales) #fma::hsales 미국 월별 주택 판매량 1973.1~1995.11
head(hsales)
```

    ##      Jan Feb Mar Apr May Jun
    ## 1973  55  60  68  63  65  61

``` r
tmp<-data.frame(date=zoo::as.Date(time(hsales)), y=as.numeric(hsales))
write.csv(tmp, 'C:/timeseries/hsales.csv', row.names=F)
tmp <- read.csv("C:/timeseries/hsales.csv", stringsAsFactors=FALSE)
tmp$date <- as.Date(tmp$date)
# xts::xts(tmp$y, tmp$date)
# ts(tmp$y, start=c(1973,1), frequency=12) 
```

### 6.1 Time Series Components

Addictive model   
![ y\_t=S\_t+T\_t+R\_t
](https://latex.codecogs.com/png.latex?%20y_t%3DS_t%2BT_t%2BR_t%20
" y_t=S_t+T_t+R_t ")  
Multiplicative model   
![ y\_t=S\_t\\times T\_t\\times R\_t
](https://latex.codecogs.com/png.latex?%20y_t%3DS_t%5Ctimes%20T_t%5Ctimes%20R_t%20
" y_t=S_t\\times T_t\\times R_t ")  
is equivalent to   
![ \\log y\_t=\\log S\_t +\\log T\_t +\\log R\_t
](https://latex.codecogs.com/png.latex?%20%5Clog%20y_t%3D%5Clog%20S_t%20%2B%5Clog%20T_t%20%2B%5Clog%20R_t%20%20
" \\log y_t=\\log S_t +\\log T_t +\\log R_t  ")  

### 6.2 Moving Average Smoothing

A moving average of order   
![m](https://latex.codecogs.com/png.latex?m "m")  
can be written as   
![ \\hat{T}\_t = \\frac{1}{m} \\sum\_{j=-k}^k{y}\_{t+j}
](https://latex.codecogs.com/png.latex?%20%5Chat%7BT%7D_t%20%3D%20%5Cfrac%7B1%7D%7Bm%7D%20%5Csum_%7Bj%3D-k%7D%5Ek%7By%7D_%7Bt%2Bj%7D%20
" \\hat{T}_t = \\frac{1}{m} \\sum_{j=-k}^k{y}_{t+j} ")  
where   
![ m=2k+1 ](https://latex.codecogs.com/png.latex?%20m%3D2k%2B1%20
" m=2k+1 ")  

``` r
library(fpp2)
autoplot(elecsales)+xlab("Year")+ylab("GWh")+ggtitle("Annual electricity sales: South Austrailia")
```

![](ts_ch6_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

### 6.3 Classical Decomposition

  - Addictive decomposition \* S1: Trend Estimation by MA, T \* S2:
    Detrended series, y-T \* S3: Seasonal component, S=simple average of
    detrended values for each season \* S4: Random component, R=(y-T)-S
  - Multiplicative decomposition \* S1: Trend Estimation by MA, T \* S2:
    Detrended series, y/T \* S3: Seasonal component, S=simple average of
    detrended values for each season \* S4: Random component, R=y/(TS)

<!-- end list -->

``` r
dc.hsales<-decompose(hsales, type="additive")
str(dc.hsales)
```

    ## List of 6
    ##  $ x       : Time-Series [1:275] from 1973 to 1996: 55 60 68 63 65 61 54 52 46 42 ...
    ##  $ seasonal: Time-Series [1:275] from 1973 to 1996: -7.01 -1.24 9.09 8.1 7.29 ...
    ##  $ trend   : Time-Series [1:275] from 1973 to 1996: NA NA NA NA NA ...
    ##  $ random  : Time-Series [1:275] from 1973 to 1996: NA NA NA NA NA ...
    ##  $ figure  : num [1:12] -7.01 -1.24 9.09 8.1 7.29 ...
    ##  $ type    : chr "additive"
    ##  - attr(*, "class")= chr "decomposed.ts"

``` r
#plot
autoplot(dc.hsales) + xlab('Year') + ggtitle('Classical Addictive Decomposition of hsales')
```

![](ts_ch6_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
dc.hsales<-decompose(hsales, type="multiplicative")
str(dc.hsales)
```

    ## List of 6
    ##  $ x       : Time-Series [1:275] from 1973 to 1996: 55 60 68 63 65 61 54 52 46 42 ...
    ##  $ seasonal: Time-Series [1:275] from 1973 to 1996: 0.865 0.977 1.171 1.149 1.142 ...
    ##  $ trend   : Time-Series [1:275] from 1973 to 1996: NA NA NA NA NA ...
    ##  $ random  : Time-Series [1:275] from 1973 to 1996: NA NA NA NA NA ...
    ##  $ figure  : num [1:12] 0.865 0.977 1.171 1.149 1.142 ...
    ##  $ type    : chr "multiplicative"
    ##  - attr(*, "class")= chr "decomposed.ts"

``` r
#plot
autoplot(dc.hsales) + xlab('Year') + ggtitle('Classical Multiplicative Decomposition of hsales')
```

![](ts_ch6_files/figure-gfm/unnamed-chunk-4-2.png)<!-- --> \#\#\# 6.4
X11 decomposition: seasonal::seas(x,x11="“) \* seasonal: R interface to
X13-ARIMA-SEATS \* seasonal::seas(x, x11=”",…): X11 decomposition \*
seas 객체 반환 \* $[data:{final](data:%7Bfinal), seasonal, seasonaladj,
trend, irregular, adjustfac}

### 6.5 SEATS decomposition: seasonal::seas(x)

  - seasonal: R interface to X13-ARIMA-SEATS \* seasonal::seas(x) \*
    seas 객체 반환 \* $[data:{final](data:%7Bfinal), seasonal, seasonaladj,
    trend, irregular, adjustfac}

### 6.6 STL decomposition: stl(x)

  - stl: stl decomposition \* stl(x, s.window, t.window=NULL, …):
    additive decomposition only \* stl 객체 반환 \*
    $[data:{final](data:%7Bfinal), seasonal, seasonaladj, trend,
    irregular, adjustfac}

### 6.7 Prediction

  - S1: Forecast seasonal component using seasonal naive method(; just
    use the last year of the estimated seasonal component)
  - S2: Forecast seasonally adjusted series with any non-seasonal
    forecasting method such as RW with drift, Holt’s LES, non-seasonal
    ARIMA, etc
  - S3 : Combine S1 and S2
  - forecast(stlobj, method=‘ets|arima|naive|rwdrift’) ◦apply a
    non-seasonal forecating to the seasadj data and reseasonalizing
    using the last year of the seasonal component
