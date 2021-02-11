The Prediction of Power Consumption in Korea
================

### Time Series Analysis

### 4\. Decomposition

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
```

  - 1.  additive

<!-- end list -->

``` r
dc.pc.add<-decompose(pc, type='additive')
str(dc.pc.add)
```

    ## List of 6
    ##  $ x       : Time-Series [1:179, 1] from 2003 to 2018: 4301 4133 4064 3885 3775 ...
    ##   ..- attr(*, "dimnames")=List of 2
    ##   .. ..$ : NULL
    ##   .. ..$ : chr "x"
    ##  $ seasonal: Time-Series [1:179] from 2003 to 2018: 484.2 302.5 34.1 -363.8 -494.3 ...
    ##  $ trend   : Time-Series [1:179, 1] from 2003 to 2018: NA NA NA NA NA ...
    ##  $ random  : Time-Series [1:179, 1] from 2003 to 2018: NA NA NA NA NA ...
    ##   ..- attr(*, "dimnames")=List of 2
    ##   .. ..$ : NULL
    ##   .. ..$ : chr "x - seasonal"
    ##  $ figure  : num [1:12] 484.2 302.5 34.1 -363.8 -494.3 ...
    ##  $ type    : chr "additive"
    ##  - attr(*, "class")= chr "decomposed.ts"

``` r
#plot
autoplot(dc.pc.add)+xlab('Month')+ggtitle('Classical additive decomposition of hsales')
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

  - 2.  Multiplicative

<!-- end list -->

``` r
dc.pc.multi<-decompose(pc, type='multiplicative')
str(dc.pc.multi)
```

    ## List of 6
    ##  $ x       : Time-Series [1:179, 1] from 2003 to 2018: 4301 4133 4064 3885 3775 ...
    ##   ..- attr(*, "dimnames")=List of 2
    ##   .. ..$ : NULL
    ##   .. ..$ : chr "x"
    ##  $ seasonal: Time-Series [1:179] from 2003 to 2018: 1.083 1.052 1.007 0.939 0.915 ...
    ##  $ trend   : Time-Series [1:179, 1] from 2003 to 2018: NA NA NA NA NA ...
    ##  $ random  : Time-Series [1:179, 1] from 2003 to 2018: NA NA NA NA NA ...
    ##   ..- attr(*, "dimnames")=List of 2
    ##   .. ..$ : NULL
    ##   .. ..$ : chr "x/seasonal"
    ##  $ figure  : num [1:12] 1.083 1.052 1.007 0.939 0.915 ...
    ##  $ type    : chr "multiplicative"
    ##  - attr(*, "class")= chr "decomposed.ts"

``` r
#plot
autoplot(dc.pc.multi)+xlab('Month')+ggtitle('Classical multiplicative decomposition of hsales')
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

### 4.1 X11 decomposition: seasonal:: seas(x, x11=’’)

``` r
library(seasonal)
ss.pc<-seas(pc, x11='')
#str(ss.pc)
head(ss.pc$data)
```

    ##             final  seasonal seasonaladj    trend irregular adjustfac
    ## Jan 2003 4059.604 1.0555603    4059.604 4036.403 1.0057481 1.0595614
    ## Feb 2003 4013.656 1.0389285    4013.656 4032.115 0.9954220 1.0297344
    ## Mar 2003 4012.486 1.0180106    4012.486 4026.891 0.9964229 1.0128883
    ## Apr 2003 4030.336 0.9614124    4030.336 4021.889 1.0021003 0.9638403
    ## May 2003 4048.071 0.9332300    4048.071 4016.997 1.0077357 0.9326418
    ## Jun 2003 4005.513 0.9779279    4005.513 4018.389 0.9967957 0.9760798

``` r
head(seasonal(ss.pc))
```

    ##            Jan       Feb       Mar       Apr       May       Jun
    ## 2003 1.0555603 1.0389285 1.0180106 0.9614124 0.9332300 0.9779279

``` r
head(trendcycle(ss.pc))
```

    ##           Jan      Feb      Mar      Apr      May      Jun
    ## 2003 4036.403 4032.115 4026.891 4021.889 4016.997 4018.389

``` r
head(remainder(ss.pc))
```

    ##            Jan       Feb       Mar       Apr       May       Jun
    ## 2003 1.0057481 0.9954220 0.9964229 1.0021003 1.0077357 0.9967957

``` r
head(seasadj(ss.pc))
```

    ##           Jan      Feb      Mar      Apr      May      Jun
    ## 2003 4059.604 4013.656 4012.486 4030.336 4048.071 4005.513

``` r
head(pc)
```

    ##         Jan    Feb    Mar    Apr    May    Jun
    ## 2003 4301.4 4133.0 4064.2 3884.6 3775.4 3909.7

``` r
# head(pc-trendcycle(ss.pc))
# 분해결과 확인  y - S*T*R = 0
sum(abs(pc - (seasonal(ss.pc)*trendcycle(ss.pc)*remainder(ss.pc))))
```

    ## [1] 4048.802

``` r
# Seasonally-adjusted data
head(seasadj(ss.pc))
```

    ##           Jan      Feb      Mar      Apr      May      Jun
    ## 2003 4059.604 4013.656 4012.486 4030.336 4048.071 4005.513

``` r
sum(abs((pc/seasonal(ss.pc))-seasadj(ss.pc)))
```

    ## [1] 3999.446

``` r
# Decomposition plot
autoplot(ss.pc) + ggtitle('X11ARIMA-SEATS multiplicative decomposition of pc')
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
# Decomposition overlay
autoplot(pc, series='y') +
  autolayer(trendcycle(ss.pc), series='trendcycle') +
  autolayer(seasadj(ss.pc), series='seasadj=y/S') +
  xlab('Month') + ylab('MW') +
  ggtitle('X11ARIMA-SEATS multiplicative decomposition: pc') +
  scale_colour_manual(values=c('gray','blue','red'), breaks=c('y','seasadj=y/S','trendcycle'))
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
# Seasonality
ggsubseriesplot(seasonal(ss.pc)) + ylab('Seasonal')  
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

### 4.2 SEATS decomposition: seasonal:: seas(x)

``` r
ssx13.pc<-seas(pc)
# str(ss.pc)
head(ssx13.pc$data)
```

    ##             final  seasonal seasonaladj    trend irregular adjustfac
    ## Jan 2003 4024.114 1.0648696    4024.114 3985.239 1.0097549 1.0689060
    ## Feb 2003 4004.862 1.0412098    4004.862 3998.382 1.0016206 1.0319955
    ## Mar 2003 4016.755 1.0169286    4016.755 4009.600 1.0017844 1.0118117
    ## Apr 2003 4041.472 0.9587631    4041.472 4019.419 1.0054868 0.9611843
    ## May 2003 4056.803 0.9312212    4056.803 4026.581 1.0075058 0.9306342
    ## Jun 2003 4009.211 0.9770259    4009.211 4031.796 0.9943983 0.9751794

``` r
head(seasonal(ssx13.pc)) 
```

    ##            Jan       Feb       Mar       Apr       May       Jun
    ## 2003 1.0648696 1.0412098 1.0169286 0.9587631 0.9312212 0.9770259

``` r
head(trendcycle(ssx13.pc))
```

    ##           Jan      Feb      Mar      Apr      May      Jun
    ## 2003 3985.239 3998.382 4009.600 4019.419 4026.581 4031.796

``` r
head(remainder(ssx13.pc))
```

    ##            Jan       Feb       Mar       Apr       May       Jun
    ## 2003 1.0097549 1.0016206 1.0017844 1.0054868 1.0075058 0.9943983

``` r
head(seasadj(ssx13.pc))
```

    ##           Jan      Feb      Mar      Apr      May      Jun
    ## 2003 4024.114 4004.862 4016.755 4041.472 4056.803 4009.211

``` r
head(hsales-trendcycle(ssx13.pc))
```

    ## Warning in .cbind.ts(list(e1, e2), c(deparse(substitute(e1))[1L],
    ## deparse(substitute(e2))[1L]), : non-intersecting series

    ## integer(0)

``` r
# 분해결과 확인  y - S*T*R = 0
#sum(abs(hsales - (seasonal(ssx13.hsales)*trendcycle(ssx13.hsales)*remainder(ssx13.hsales))))

# Decomposition plot
autoplot(ssx13.pc) + ggtitle("X13ARIMA-SEATS multiplicative decomposition of pc")
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
# Decomposition overlay
autoplot(pc, series='y') +
  autolayer(trendcycle(ssx13.pc), series='trendcycle') +
  autolayer(seasadj(ssx13.pc), series='seasadj=y/S') +
  xlab('Month') + ylab('MW') +
  ggtitle('X13ARIMA-SEATS multiplicative decomposition: pc') +
  scale_colour_manual(values=c('gray','blue','red'), breaks=c('y','seasadj=y/S','trendcycle'))
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

``` r
# Seasonality
ggsubseriesplot(seasonal(ssx13.pc)) + ylab('Seasonal(X13)')  
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-7-3.png)<!-- -->

### 4.3 STL Decomposition

``` r
pc.stl<-stl(pc[,1], t.window=13, s.window='periodic', robust=T)
autoplot(pc.stl)
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
head(seasonal(pc.stl))
```

    ##             Jan        Feb        Mar        Apr        May        Jun
    ## 2003  472.36095  272.63911   47.92661 -332.02270 -476.71920 -114.48584

``` r
head(trendcycle(pc.stl))
```

    ##           Jan      Feb      Mar      Apr      May      Jun
    ## 2003 3910.739 3939.579 3968.418 3996.068 4023.718 4052.320

``` r
head(remainder(pc.stl))
```

    ##            Jan       Feb       Mar       Apr       May       Jun
    ## 2003 -81.70008 -79.21790  47.85493 220.55449 228.40124 -28.13464

``` r
head(seasadj(pc.stl))
```

    ##           Jan      Feb      Mar      Apr      May      Jun
    ## 2003 3829.039 3860.361 4016.273 4216.623 4252.119 4024.186

``` r
# 분해결과 확인  y - (S+T+R) = 0
sum(abs(pc - (seasonal(pc.stl)+trendcycle(pc.stl)+remainder(pc.stl))))
```

    ## [1] 3.183231e-12

  - only univariate series are allowed stl 이라는 메세지가 나오면 간단하게 \[,1\]을 써주어
    첫번째 열만 써주면 된다.

#### log 변환 후 STL Decomposition

``` r
lpc<-log(pc)
lpc.stl<-stl(lpc[,1], s.window='periodic', t.window=13)
autoplot(lpc.stl)
```

![](ts_powerprediction_04decomposition_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
head(seasonal(lpc.stl))
```

    ##               Jan          Feb          Mar          Apr          May
    ## 2003  0.076674192  0.046495138  0.003719254 -0.064363307 -0.089478022
    ##               Jun
    ## 2003 -0.021293838

``` r
head(trendcycle(lpc.stl))
```

    ##           Jan      Feb      Mar      Apr      May      Jun
    ## 2003 8.297063 8.297327 8.297592 8.298405 8.299219 8.300985

``` r
head(remainder(lpc.stl))
```

    ##               Jan          Feb          Mar          Apr          May
    ## 2003 -0.007041724 -0.017063802  0.008661354  0.030733139  0.026520300
    ##               Jun
    ## 2003 -0.008474804

``` r
head(seasadj(lpc.stl))
```

    ##           Jan      Feb      Mar      Apr      May      Jun
    ## 2003 8.290022 8.280264 8.306253 8.329139 8.325740 8.292510

``` r
# 분해결과 확인  y - (S+T+R) = 0
sum(abs(pc - (seasonal(lpc.stl)+trendcycle(lpc.stl)+remainder(lpc.stl))))
```

    ## [1] 1011472

### 4.4 Forecasting with decomposition

``` r
yhat<-forecast(pc.stl, method='naive')
```
