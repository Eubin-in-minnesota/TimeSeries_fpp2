Ch05 Linear Regression Models
================

### 5.1 The Linear Model

  - Simple linear regression \*
    ![y\_t=\\beta\_0+\\beta\_1x\_t+\\epsilon\_t](https://latex.codecogs.com/png.latex?y_t%3D%5Cbeta_0%2B%5Cbeta_1x_t%2B%5Cepsilon_t
    "y_t=\\beta_0+\\beta_1x_t+\\epsilon_t")

#### Example: US Consumption expenditure

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
autoplot(uschange[,c("Consumption","Income")]) +
  ylab("% change") + xlab("Year")
```

![](ts_ch5_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->
![\\hat{y}=0.55+0.28x\_t](https://latex.codecogs.com/png.latex?%5Chat%7By%7D%3D0.55%2B0.28x_t
"\\hat{y}=0.55+0.28x_t")

``` r
uschange %>%
  as.data.frame %>%
  ggplot(aes(x=Income, y=Consumption)) +
    ylab("Consumption (quarterly % change)") +
    xlab("Income (quarterly % change)") +
    geom_point() +
    geom_smooth(method="lm", se=FALSE)
```

![](ts_ch5_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
tslm(Consumption ~ Income, data=uschange)
```

    ## 
    ## Call:
    ## tslm(formula = Consumption ~ Income, data = uschange)
    ## 
    ## Coefficients:
    ## (Intercept)       Income  
    ##      0.5451       0.2806

  - Multiple Linear Regression \*
    ![y\_t=\\beta\_0+\\beta\_1x\_{1,t}+\\beta\_2x\_{2,t}+...+\\beta\_kx\_{k,t}+\\epsilon\_t](https://latex.codecogs.com/png.latex?y_t%3D%5Cbeta_0%2B%5Cbeta_1x_%7B1%2Ct%7D%2B%5Cbeta_2x_%7B2%2Ct%7D%2B...%2B%5Cbeta_kx_%7Bk%2Ct%7D%2B%5Cepsilon_t
    "y_t=\\beta_0+\\beta_1x_{1,t}+\\beta_2x_{2,t}+...+\\beta_kx_{k,t}+\\epsilon_t")

#### Example: US Consumption expenditure(revisited)

``` r
#uschange %>%
#  as.data.frame %>%
#  GGally::ggpairs()
```

#### Assumptions

1.  we assume that the model is a **reasonable approximation** to
    reality; that is, that the relationship between the forecast
    variable and the predictor variables satisfies this linear equation.

2.  Second, we make the following assumptions about the errors (ε1,…,εT)
    (ε1,…,εT) :
    
      - they have mean zero; otherwise the forecasts will be
        systematically biased.
      - they are not autocorrelated; otherwise the forecasts will be
        inefficient as there is more information to be exploited in the
        data.
      - they are unrelated to the predictor variables; otherwise there
        would be more information that should be included in the
        systematic part of the model.

### 5.2 Least Square Estimation

![\\sum\_{t=1}^T \\varepsilon\_t^2 = \\sum\_{t=1}^T (y\_t - \\beta\_{0}
- \\beta\_{1} x\_{1,t} - \\beta\_{2} x\_{2,t} - \\cdots - \\beta\_{k}
x\_{k,t})^2](https://latex.codecogs.com/png.latex?%5Csum_%7Bt%3D1%7D%5ET%20%5Cvarepsilon_t%5E2%20%3D%20%5Csum_%7Bt%3D1%7D%5ET%20%28y_t%20-%20%20%5Cbeta_%7B0%7D%20-%20%5Cbeta_%7B1%7D%20x_%7B1%2Ct%7D%20-%20%5Cbeta_%7B2%7D%20x_%7B2%2Ct%7D%20-%20%5Ccdots%20-%20%5Cbeta_%7Bk%7D%20x_%7Bk%2Ct%7D%29%5E2
"\\sum_{t=1}^T \\varepsilon_t^2 = \\sum_{t=1}^T (y_t -  \\beta_{0} - \\beta_{1} x_{1,t} - \\beta_{2} x_{2,t} - \\cdots - \\beta_{k} x_{k,t})^2")

#### Example: US Consumption expenditure(revisited)

![y\_t=\\beta\_0 + \\beta\_1 x\_{1,t}+ \\beta\_2 x\_{2,t}+ \\beta\_3
x\_{3,t}+ \\beta\_4
x\_{4,t}+\\varepsilon\_t](https://latex.codecogs.com/png.latex?y_t%3D%5Cbeta_0%20%2B%20%5Cbeta_1%20x_%7B1%2Ct%7D%2B%20%5Cbeta_2%20x_%7B2%2Ct%7D%2B%20%5Cbeta_3%20x_%7B3%2Ct%7D%2B%20%5Cbeta_4%20x_%7B4%2Ct%7D%2B%5Cvarepsilon_t
"y_t=\\beta_0 + \\beta_1 x_{1,t}+ \\beta_2 x_{2,t}+ \\beta_3 x_{3,t}+ \\beta_4 x_{4,t}+\\varepsilon_t")

``` r
fit.consMR <- tslm(Consumption ~ Income + Production + Unemployment + Savings,
  data=uschange)
summary(fit.consMR)
```

    ## 
    ## Call:
    ## tslm(formula = Consumption ~ Income + Production + Unemployment + 
    ##     Savings, data = uschange)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.88296 -0.17638 -0.03679  0.15251  1.20553 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   0.26729    0.03721   7.184 1.68e-11 ***
    ## Income        0.71449    0.04219  16.934  < 2e-16 ***
    ## Production    0.04589    0.02588   1.773   0.0778 .  
    ## Unemployment -0.20477    0.10550  -1.941   0.0538 .  
    ## Savings      -0.04527    0.00278 -16.287  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.3286 on 182 degrees of freedom
    ## Multiple R-squared:  0.754,  Adjusted R-squared:  0.7486 
    ## F-statistic: 139.5 on 4 and 182 DF,  p-value: < 2.2e-16

#### Fitted values

$\_t=\_0+*1x*{1,t}+*2x*{2,t}+…+*kx*{k,t} $

``` r
autoplot(uschange[,'Consumption'], series="Data") +
  forecast::autolayer(fitted(fit.consMR), series="Fitted") +
  xlab("Year") + ylab("") +
  ggtitle("Percentage change in US consumption expenditure") +
  guides(colour=guide_legend(title=" "))
```

![](ts_ch5_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
cbind(Data=uschange[,"Consumption"], Fitted=fitted(fit.consMR)) %>%
  as.data.frame %>%
  ggplot(aes(x=Data, y=Fitted)) +
    geom_point() +
    xlab("Fitted (predicted values)") +
    ylab("Data (actual values)") +
    ggtitle("Percentage change in US consumption expenditure") +
    geom_abline(intercept=0, slope=1)
```

![](ts_ch5_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

### 5.3 Some Useful Predictors

#### Trend

using
![x\_{1,t}=t](https://latex.codecogs.com/png.latex?x_%7B1%2Ct%7D%3Dt
"x_{1,t}=t") as a predictor $y\_t=\_0+\_1t+\_t
$

#### Dummy varibles

| day       | ![d\_{1,t}](https://latex.codecogs.com/png.latex?d_%7B1%2Ct%7D "d_{1,t}") | ![d\_{2,t}](https://latex.codecogs.com/png.latex?d_%7B2%2Ct%7D "d_{2,t}") | ![d\_{3,t}](https://latex.codecogs.com/png.latex?d_%7B3%2Ct%7D "d_{3,t}") | ![d\_{4,t}](https://latex.codecogs.com/png.latex?d_%7B4%2Ct%7D "d_{4,t}") | ![d\_{5,t}](https://latex.codecogs.com/png.latex?d_%7B5%2Ct%7D "d_{5,t}") | ![d\_{6,t}](https://latex.codecogs.com/png.latex?d_%7B6%2Ct%7D "d_{6,t}") |
| --------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| Monday    | 1                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         |
| Tuesday   | 0                                                                         | 1                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         |
| Wednesday | 0                                                                         | 0                                                                         | 1                                                                         | 0                                                                         | 0                                                                         | 0                                                                         |
| Thursday  | 0                                                                         | 0                                                                         | 0                                                                         | 1                                                                         | 0                                                                         | 0                                                                         |
| Friday    | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 1                                                                         | 0                                                                         |
| Saturday  | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 1                                                                         |
| Sunday    | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         |
| Monday    | 1                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         |
| Tuesday   | 0                                                                         | 1                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         |
| Wednesday | 0                                                                         | 0                                                                         | 1                                                                         | 0                                                                         | 0                                                                         | 0                                                                         |
| Thursday  | 0                                                                         | 0                                                                         | 0                                                                         | 1                                                                         | 0                                                                         | 0                                                                         |
| Friday    | 0                                                                         | 0                                                                         | 0                                                                         | 0                                                                         | 1                                                                         | 0                                                                         |

#### Examples: Australian quarterly beer production

``` r
beer2 <- window(ausbeer, start=1992)
autoplot(beer2) + xlab("Year") + ylab("Megalitres")
```

![](ts_ch5_files/figure-gfm/unnamed-chunk-6-1.png)<!-- --> Model:
![y\_t=\\beta\_0+\\beta\_1t+\\beta\_2d\_{2,t}+\\beta\_3d\_{3,t}+\\beta\_4d\_{4,t}+\\epsilon\_t](https://latex.codecogs.com/png.latex?y_t%3D%5Cbeta_0%2B%5Cbeta_1t%2B%5Cbeta_2d_%7B2%2Ct%7D%2B%5Cbeta_3d_%7B3%2Ct%7D%2B%5Cbeta_4d_%7B4%2Ct%7D%2B%5Cepsilon_t
"y_t=\\beta_0+\\beta_1t+\\beta_2d_{2,t}+\\beta_3d_{3,t}+\\beta_4d_{4,t}+\\epsilon_t")

``` r
fit.beer <- tslm(beer2 ~ trend + season)
summary(fit.beer)
```

    ## 
    ## Call:
    ## tslm(formula = beer2 ~ trend + season)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -42.903  -7.599  -0.459   7.991  21.789 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 441.80044    3.73353 118.333  < 2e-16 ***
    ## trend        -0.34027    0.06657  -5.111 2.73e-06 ***
    ## season2     -34.65973    3.96832  -8.734 9.10e-13 ***
    ## season3     -17.82164    4.02249  -4.430 3.45e-05 ***
    ## season4      72.79641    4.02305  18.095  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 12.23 on 69 degrees of freedom
    ## Multiple R-squared:  0.9243, Adjusted R-squared:  0.9199 
    ## F-statistic: 210.7 on 4 and 69 DF,  p-value: < 2.2e-16

``` r
autoplot(beer2, series="Data") +
  forecast::autolayer(fitted(fit.beer), series="Fitted") +
  xlab("Year") + ylab("Megalitres") +
  ggtitle("Quarterly Beer Production")
```

![](ts_ch5_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
cbind(Data=beer2, Fitted=fitted(fit.beer)) %>%
  as.data.frame %>%
  ggplot(aes(x=Data, y=Fitted, colour=as.factor(cycle(beer2)))) +
    geom_point() +
    ylab("Fitted") + xlab("Actual values") +
    ggtitle("Quarterly beer production") +
    scale_colour_brewer(palette="Dark2", name="Quarter") +
    geom_abline(intercept=0, slope=1)
```

![](ts_ch5_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

#### Fourier Series

![x\_{1,t} = \\sin\\left(\\textstyle\\frac{2\\pi t}{m}\\right), x\_{2,t}
= \\cos\\left(\\textstyle\\frac{2\\pi t}{m}\\right), x\_{3,t} =
\\sin\\left(\\textstyle\\frac{4\\pi t}{m}\\right), x\_{4,t} =
\\cos\\left(\\textstyle\\frac{4\\pi t}{m}\\right), x\_{5,t} =
\\sin\\left(\\textstyle\\frac{6\\pi t}{m}\\right), x\_{6,t} =
\\cos\\left(\\textstyle\\frac{6\\pi
t}{m}\\right),](https://latex.codecogs.com/png.latex?x_%7B1%2Ct%7D%20%3D%20%5Csin%5Cleft%28%5Ctextstyle%5Cfrac%7B2%5Cpi%20t%7D%7Bm%7D%5Cright%29%2C%20%20x_%7B2%2Ct%7D%20%3D%20%5Ccos%5Cleft%28%5Ctextstyle%5Cfrac%7B2%5Cpi%20t%7D%7Bm%7D%5Cright%29%2C%20%20x_%7B3%2Ct%7D%20%3D%20%5Csin%5Cleft%28%5Ctextstyle%5Cfrac%7B4%5Cpi%20t%7D%7Bm%7D%5Cright%29%2C%20%20x_%7B4%2Ct%7D%20%3D%20%5Ccos%5Cleft%28%5Ctextstyle%5Cfrac%7B4%5Cpi%20t%7D%7Bm%7D%5Cright%29%2C%20%20x_%7B5%2Ct%7D%20%3D%20%5Csin%5Cleft%28%5Ctextstyle%5Cfrac%7B6%5Cpi%20t%7D%7Bm%7D%5Cright%29%2C%20%20x_%7B6%2Ct%7D%20%3D%20%5Ccos%5Cleft%28%5Ctextstyle%5Cfrac%7B6%5Cpi%20t%7D%7Bm%7D%5Cright%29%2C
"x_{1,t} = \\sin\\left(\\textstyle\\frac{2\\pi t}{m}\\right),  x_{2,t} = \\cos\\left(\\textstyle\\frac{2\\pi t}{m}\\right),  x_{3,t} = \\sin\\left(\\textstyle\\frac{4\\pi t}{m}\\right),  x_{4,t} = \\cos\\left(\\textstyle\\frac{4\\pi t}{m}\\right),  x_{5,t} = \\sin\\left(\\textstyle\\frac{6\\pi t}{m}\\right),  x_{6,t} = \\cos\\left(\\textstyle\\frac{6\\pi t}{m}\\right),")

``` r
fourier.beer <- tslm(beer2 ~ trend + fourier(beer2, K=2))
summary(fourier.beer)
```

    ## 
    ## Call:
    ## tslm(formula = beer2 ~ trend + fourier(beer2, K = 2))
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -42.903  -7.599  -0.459   7.991  21.789 
    ## 
    ## Coefficients:
    ##                            Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)               446.87920    2.87321 155.533  < 2e-16 ***
    ## trend                      -0.34027    0.06657  -5.111 2.73e-06 ***
    ## fourier(beer2, K = 2)S1-4   8.91082    2.01125   4.430 3.45e-05 ***
    ## fourier(beer2, K = 2)C1-4  53.72807    2.01125  26.714  < 2e-16 ***
    ## fourier(beer2, K = 2)C2-4  13.98958    1.42256   9.834 9.26e-15 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 12.23 on 69 degrees of freedom
    ## Multiple R-squared:  0.9243, Adjusted R-squared:  0.9199 
    ## F-statistic: 210.7 on 4 and 69 DF,  p-value: < 2.2e-16

#### Trading days

#### Distributed lags

#### Easter

### 5.4 Evaluating the Regression Model

![e\_t &= y\_t - \\hat{y}\_t \\\\ &= y\_t - \\hat\\beta\_{0} -
\\hat\\beta\_{1} x\_{1,t} - \\hat\\beta\_{2} x\_{2,t} - \\cdots -
\\hat\\beta\_{k}
x\_{k,t}](https://latex.codecogs.com/png.latex?e_t%20%26%3D%20y_t%20-%20%5Chat%7By%7D_t%20%5C%5C%20%20%26%3D%20y_t%20-%20%5Chat%5Cbeta_%7B0%7D%20-%20%5Chat%5Cbeta_%7B1%7D%20x_%7B1%2Ct%7D%20-%20%5Chat%5Cbeta_%7B2%7D%20x_%7B2%2Ct%7D%20-%20%5Ccdots%20-%20%5Chat%5Cbeta_%7Bk%7D%20x_%7Bk%2Ct%7D
"e_t &= y_t - \\hat{y}_t \\\\  &= y_t - \\hat\\beta_{0} - \\hat\\beta_{1} x_{1,t} - \\hat\\beta_{2} x_{2,t} - \\cdots - \\hat\\beta_{k} x_{k,t}")

#### ACF plot of residuals

#### Histogram of residuals

``` r
#잔차 확인
checkresiduals(fit.beer)
```

![](ts_ch5_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

    ## 
    ##  Breusch-Godfrey test for serial correlation of order up to 8
    ## 
    ## data:  Residuals from Linear regression model
    ## LM test = 9.3083, df = 8, p-value = 0.317

#### Residual plots against predictors

``` r
library(gridExtra)
```

#### Residual plots against fitted values

``` r
# 적합값과 잔차 확ㅇ
fit <- tslm(elec ~ trend + season)
cbind(Fitted=fitted(fit), Residuals=residuals(fit)) %>%
  as.data.frame %>%
  ggplot(aes(x=Fitted, y=Residuals)) + geom_point()
```

![](ts_ch5_files/figure-gfm/unnamed-chunk-11-1.png)<!-- --> \#\#\#\#
Outliers and influential observations \#\#\#\# Goodness-of-Fit ![R^2 =
\\frac{\\sum(\\hat{y}\_{t} -
\\bar{y})^2}{\\sum(y\_{t}-\\bar{y})^2}](https://latex.codecogs.com/png.latex?R%5E2%20%3D%20%5Cfrac%7B%5Csum%28%5Chat%7By%7D_%7Bt%7D%20-%20%5Cbar%7By%7D%29%5E2%7D%7B%5Csum%28y_%7Bt%7D-%5Cbar%7By%7D%29%5E2%7D
"R^2 = \\frac{\\sum(\\hat{y}_{t} - \\bar{y})^2}{\\sum(y_{t}-\\bar{y})^2}")

``` r
fit.consMR <- tslm(Consumption ~ Income + Production + Unemployment + Savings,
  data=uschange)
summary(fit.consMR)
```

    ## 
    ## Call:
    ## tslm(formula = Consumption ~ Income + Production + Unemployment + 
    ##     Savings, data = uschange)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.88296 -0.17638 -0.03679  0.15251  1.20553 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   0.26729    0.03721   7.184 1.68e-11 ***
    ## Income        0.71449    0.04219  16.934  < 2e-16 ***
    ## Production    0.04589    0.02588   1.773   0.0778 .  
    ## Unemployment -0.20477    0.10550  -1.941   0.0538 .  
    ## Savings      -0.04527    0.00278 -16.287  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.3286 on 182 degrees of freedom
    ## Multiple R-squared:  0.754,  Adjusted R-squared:  0.7486 
    ## F-statistic: 139.5 on 4 and 182 DF,  p-value: < 2.2e-16

#### Standard error of the regression

![\\hat{\\sigma}\_e=\\sqrt{\\frac{1}{T-k-1}\\sum\_{t=1}^{T}{e\_t^2}}](https://latex.codecogs.com/png.latex?%5Chat%7B%5Csigma%7D_e%3D%5Csqrt%7B%5Cfrac%7B1%7D%7BT-k-1%7D%5Csum_%7Bt%3D1%7D%5E%7BT%7D%7Be_t%5E2%7D%7D
"\\hat{\\sigma}_e=\\sqrt{\\frac{1}{T-k-1}\\sum_{t=1}^{T}{e_t^2}}")

#### Spurious regression 허위 상관

  - 인과관계가 없는 두 변수가 높은 상관관계를 가지는 경우. 보통 제 3의 변수(lurking variable,
    confounding variable)의 변수에 의한 효과일 가능성이 높음
  - 황새 수와 출생아 수, 껌판매량과 범죄건수 ![그래프](/timeseries/lurker.jpg)
  - 설명모형으로는 쓸 수 없지만, 예측 목적으로 사용 가능
  - 두 시계열이 강한 자기 상관관계나 계절성을 갖는 경우 또는 두 비정상 시계열이 (결정적 또는 확률적) 추세를 갖는 경우가
    많음

<!-- end list -->

``` r
#fit<-tslm(ausair, start=1970, end=2009)
```
