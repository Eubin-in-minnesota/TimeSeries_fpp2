Chapter2 Time Series graphics
================

  - 참고 사이트
      - [fpp2](https://www.otexts.org/fpp2/)
      - [prof’s
        page](http://202.20.119.71/~dongg/rfpp2/fpp2-Ch2TimeSeriesGraphics.html)

## Chapter2 Time Series graphics

### 2.1 ts objects

  - ts function: ts(data, start=n, frequency=n)
      - data: 벡터, 행렬 등 등간격 시계열 자료
      - start: 시작점(별 옵션 없으면 1씩 증가)
      - frequency: 빈도(1=1년, 4=4분기, 12=12개월, 52=52주)
  - time(y): y의 이름(날짜) 리턴

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
y<-ts(c(123,39,78,52,110), start=2012)
z<-c(1,2,3)
y<-ts(z, start=2003, frequency=12)
y
```

    ##      Jan Feb Mar
    ## 2003   1   2   3

``` r
time(y)
```

    ##           Jan      Feb      Mar
    ## 2003 2003.000 2003.083 2003.167

### 2.2 Time Plots

  - 시계열 그래프 볼 때 확인해야할 점 3가지
      - 1.  수평인지 아닌지
    
      - 2.  트렌드가 있는지
    
      - 3.  계절성(일정한 간격으로 패턴이 유지되는지)
  - autoplot function: 단변량(변수가 하나) 찍을때 사용.
  - xlab(‘이름’): x축 label
  - ylab(‘이름’): y축 label

<!-- end list -->

``` r
head(melsyd)
```

    ## Time Series:
    ## Start = c(1987, 26) 
    ## End = c(1987, 31) 
    ## Frequency = 52 
    ##          First.Class Business.Class Economy.Class
    ## 1987.481       1.912             NA        20.167
    ## 1987.500       1.848             NA        20.161
    ## 1987.519       1.856             NA        19.993
    ## 1987.538       2.142             NA        20.986
    ## 1987.558       2.118             NA        20.497
    ## 1987.577       2.048             NA        20.770

``` r
#melsyd set의 이코노미 변수만 추출
autoplot(melsyd[,"Economy.Class"]) + ggtitle("Economy class passengers: Melbourne-Sydney")+xlab("year") + ylab("Thousands")
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
autoplot(a10)+ggtitle("Antidiabetic drug sales")+ylab("$ million")+xlab("Year")
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

### 2.3 Time Series patterns

  - Trend(경향)
      - 기간과 관련 없는 패턴
      - a long-term increase or decrease 전반적으로 증가/감소 추세인지
  - Seasonal(주기성)
      - 특정 패턴이 지속되는지
  - Cyclic(순환변동)
      - 최소 2년을 주기로 패턴이 반복되는지
  - 정상 vs 비정상

| 정상      | 비정상        |
| ------- | ---------- |
| 그래프가 수평 | 수평 아님      |
| 등분산     | 등분산 아님     |
| 폭이 일정   | 폭이 일정하지 않음 |

  - 비정상 그래프를 정상화 =\> 수평으로 만듦

### 2.4 Seasonal Plots 주기/계절성 확인

  - a10 data set: monthly antidiabetic drug sales in
Austrailia

<!-- end list -->

``` r
ggseasonplot(a10, year.labels=TRUE, year.labels.left=TRUE)+ylab("$ million")+ggtitle("Seasonal plot: antidiabetic drug sales")
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
ggseasonplot(a10, polar=TRUE)+ylab("$ million")+ggtitle("Polar seasonal plot: antidiabetic drug sales")
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->

``` r
#polar=TRUE; 극좌표 모양으로 나타내기
```

### 2.5 Seasonal subseries plots 적은 주기 간격으로 상세히 나타내기

``` r
ggsubseriesplot(a10)+ylab("$ million")+ggtitle("Seasonal subseries plot: antidiabetic drug sales")
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

### 2.6 Scatterplots

  - motel data set: monthly takings(in $million) from accommodation

<!-- end list -->

``` r
# motel[,2:1]: motel 데이터셋의 변수 2개 나타내기
# facet=TRUE: 두 변수를 각각 따로 그래프로 나타내라.
autoplot(motel[,2:1]/1000, facet=TRUE)+xlab("Year")+ylab("")+ggtitle("Total monthly accomodation: Victoria, Austrailia")
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
qplot(Roomnights/1000, Takings/1000, data=as.data.frame(motel))+ylab("Takings ($million)")+xlab("Room nights(thousands)")
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

### 2.7 Lag plots

  - lag:
시차

<!-- end list -->

``` r
beer2<-window(ausbeer, start=1992) #window함수; 시계열 일부 데이터만 가져올때 사용 #92년도부터 끊기
gglagplot(beer2)
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

### 2.8 Autocorrelation 자기상관

| lag  | 1960.1 | 1960.2 | 1960.3 | 1960.4 | 1960.5 |
| ---- | ------ | ------ | ------ | ------ | ------ |
| lag1 | 500    | 400    | 300    | 501    | 350    |
| lag2 | 400    | 300    | 501    | 350    |        |
| lag3 | 300    | 501    | 350    |        |        |
| lag4 | 501    | 350    |        |        |        |
| lag5 | 350    |        |        |        |        |

  - 자기 상관이 0인지 아닌지 중요
  - \-1\< ACF \< 1
      - ACF=1: 이전 시차와 기준 시점은 양의 상관
      - ACF=0: 이전 시차와 기준 시점은 상관 없음
      - ACF=-1: 이전 시차와 기준 시점은 음의 상관

<!-- end list -->

``` r
#
ggAcf(beer2)
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
aelec<-window(elec, start=1980)
autoplot(aelec)+xlab("Year")+ylab("GWh")
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

``` r
ggAcf(aelec, lag=48) 
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-7-3.png)<!-- -->

``` r
# 4년치 자기상관
# 계절성 패턴 존재
```

### 2.9 White noise 오차항

  - 회귀분석 시 오차항의 특징(가정)
      - 1.  자기 상관 없음(독립성)
    
      - 2.  등분산
    
      - 3.  정규분포

<!-- end list -->

``` r
set.seed(30)
y<-ts(rnorm(50))
autoplot(y)+ggtitle("White noise")
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
ggAcf(y)
```

![](ts_ch2_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->
