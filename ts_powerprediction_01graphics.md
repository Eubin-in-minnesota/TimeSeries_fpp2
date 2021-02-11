The Prediction of Power Consumption in Korea
================

### Time Series Analysis

### 1\. Time Series Graphics

#### Last cleansing; sort ‘date’ by decreasing

``` r
# 'date'-'y'데이터에서 'date' 오름차순 정렬 후 'date' 열 삭제
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
data<-read.csv('C:/timeseries/powerdata.csv', header=T, sep=",", stringsAsFactors=F)
mid<-data[order(data$date, decreasing = F),]
powercm<-subset(mid,select=c('y'))
powerc<-powercm[1:179,]
#powerc<-as.data.frame(powerc)
```

#### 1.1 Making ts object

``` r
pc<-ts(powerc, start=2003, frequency = 12)
#ex<-write.csv(pc, 'C:/timeseries/powerExampleData.csv', sep=",", row.names = FALSE)
head(pc)
```

    ##         Jan    Feb    Mar    Apr    May    Jun
    ## 2003 4301.4 4133.0 4064.2 3884.6 3775.4 3909.7

``` r
str(pc)
```

    ##  Time-Series [1:179] from 2003 to 2018: 4301 4133 4064 3885 3775 ...

``` r
colnames(pc)
```

    ## NULL

#### 1.2 Time Plots and Time Series Patterns

``` r
autoplot(pc)+ggtitle("Average of Monthly Power Consumption in Korea: 2003/01 - 2017/11")+xlab("month")+ylab("MW")
```

![](ts_powerprediction_01graphics_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

  - Trend: increasing
  - Seasonal: True
  - Cyclic: probably True

Conc: seems non-stationary
data

#### 1.3 Seasonal Plots 주기/계절성 확인

``` r
ggseasonplot(pc, year.labels=T, year.labels.left=T)+ylab('MW')+ggtitle('Seasonal Plot: Power Consumption')
```

![](ts_powerprediction_01graphics_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
# 극좌표 모양
ggseasonplot(pc, polar=T)+ylab('MW')+ggtitle('Polar Seasonal Plot: Power Consumption')
```

![](ts_powerprediction_01graphics_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->

``` r
# 적은 주기 간격으로 상세히 나타내기
ggsubseriesplot(pc)+ylab('MW')+ggtitle('Seasonal Subseries Plot: Average of Monthly Power Consumption')
```

![](ts_powerprediction_01graphics_files/figure-gfm/unnamed-chunk-4-3.png)<!-- -->

``` r
# Scatterplot; pc 데이터는 변수가 하나이므로 해당 없음
```

#### 1.4 Autocorrealtion 자기상관

``` r
ggAcf(pc)
```

![](ts_powerprediction_01graphics_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

  - 신뢰구간 내 0이 포함되지 않아 아주 강한 자기 상관을 가지고 있다.
  - Seasonal: True 계절성 패턴이 존재함을 확인할 수 있다.
