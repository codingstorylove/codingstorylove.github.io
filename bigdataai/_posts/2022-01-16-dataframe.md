---
layout: single
title: "범주형 데이터 , 연속형 데이터로 분석 및 교차분석"
date: "2022-01-16 17:01:00 +0900"
last_modified_at: "2022-01-16 17:34:00 +0900"
---


# 범주형 데이터, 연속형 데이터 으로 분석하기

1. 독립변수가 범주형자료이고 종속변수가 범주형자료일때 교차분석을 한다

- 교차분석이란?
- 참고사이트 : [https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=aporia25&logNo=221156141366](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=aporia25&logNo=221156141366)


교차분석(cross-tabulation analysis)은 '범주형'으로 구성된 자료들 간의 연관관계를 확인하기 위해 교차표를 만들어 관계를 확인하는 분석 방법을 말한다. 이 방법에서는 변수들의 빈도를 이용하여 연관성을 파악하는데, 이 때 검정통계량으로 카이제곱($\chi^2$) 통계량을 이용한다. 이 때문에 교차분석은 카이제곱($\chi^2$) 검정이라고도 불린다.

귀무가설: 기대도수와 관측도수 간에 차이가 없다

대립가설: 기대도수와 관측도수 간에 차이가 있다



![expe]({{ site.gdrive_url_prefix }}1Ck6M7oltpHBp3ainD8OFCOY_f-8Gzse-)


2. 독립변수가 범주형자료이고 종속변수가 연속형자료일때 t-test 또는 분산분석(ANOVA)을 한다

- 독립변수 범주가 2개일때 t-test

- 독립변수 범주가 3개 이상일때 분산분석

3. 독립변수가 연속형자료이고 종속변수가 연속형자료일때 상관관계분석 또는
   선형회귀분석을 한다

4. 독립변수가 연속형자료이고 종속변수가 범주형자료일때 로지스틱 회귀분석 또는
   판별분석 또는 군집분석을 한다


```python
# 실습 예제
a <- read.csv('cosmetics.csv', header = T, sep = ',')
head(a)
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>marriage</th><th scope=col>edu</th><th scope=col>job</th><th scope=col>mincome</th><th scope=col>aware</th><th scope=col>count</th><th scope=col>amount</th><th scope=col>decision</th><th scope=col>propensity</th><th scope=col>skin</th><th scope=col>promo</th><th scope=col>location</th><th scope=col>satisf_b</th><th scope=col>satisf_i</th><th scope=col>satisf_al</th><th scope=col>repurchase</th></tr></thead>
<tbody>
	<tr><td>1     </td><td>1     </td><td>4     </td><td>1     </td><td>2     </td><td>2     </td><td>1     </td><td> 11000</td><td>2     </td><td>1     </td><td>1     </td><td>1     </td><td>2     </td><td>5     </td><td>2     </td><td>2     </td><td>2     </td></tr>
	<tr><td>2     </td><td>1     </td><td>4     </td><td>9     </td><td>2     </td><td>1     </td><td>4     </td><td> 30000</td><td>1     </td><td>1     </td><td>3     </td><td>2     </td><td>3     </td><td>2     </td><td>3     </td><td>3     </td><td>4     </td></tr>
	<tr><td>2     </td><td>2     </td><td>4     </td><td>4     </td><td>3     </td><td>1     </td><td>6     </td><td>100000</td><td>3     </td><td>2     </td><td>3     </td><td>2     </td><td>2     </td><td>4     </td><td>5     </td><td>4     </td><td>4     </td></tr>
	<tr><td>2     </td><td>2     </td><td>4     </td><td>7     </td><td>5     </td><td>2     </td><td>6     </td><td> 65000</td><td>3     </td><td>2     </td><td>5     </td><td>2     </td><td>3     </td><td>3     </td><td>4     </td><td>4     </td><td>4     </td></tr>
	<tr><td>1     </td><td>2     </td><td>6     </td><td>6     </td><td>5     </td><td>2     </td><td>2     </td><td> 50000</td><td>2     </td><td>2     </td><td>3     </td><td>2     </td><td>3     </td><td>3     </td><td>3     </td><td>3     </td><td>3     </td></tr>
	<tr><td>2     </td><td>2     </td><td>2     </td><td>7     </td><td>3     </td><td>1     </td><td>2     </td><td>100000</td><td>2     </td><td>1     </td><td>4     </td><td>2     </td><td>3     </td><td>3     </td><td>4     </td><td>4     </td><td>3     </td></tr>
</tbody>
</table>




```python
str(a)
```

    'data.frame':	247 obs. of  17 variables:
     $ gender    : int  1 2 2 2 1 2 2 1 2 2 ...
     $ marriage  : int  1 1 2 2 2 2 1 1 2 2 ...
     $ edu       : int  4 4 4 4 6 2 6 6 4 4 ...
     $ job       : int  1 9 4 7 6 7 4 4 5 5 ...
     $ mincome   : int  2 2 3 5 5 3 5 5 2 2 ...
     $ aware     : int  2 1 1 2 2 1 1 4 2 1 ...
     $ count     : int  1 4 6 6 2 2 5 10 2 2 ...
     $ amount    : int  11000 30000 100000 65000 50000 100000 100000 39000 40000 100000 ...
     $ decision  : int  2 1 3 3 2 2 3 3 3 3 ...
     $ propensity: int  1 1 2 2 2 1 2 2 2 3 ...
     $ skin      : int  1 3 3 5 3 4 5 2 3 3 ...
     $ promo     : int  1 2 2 2 2 2 2 1 2 1 ...
     $ location  : int  2 3 2 3 3 3 3 2 3 3 ...
     $ satisf_b  : int  5 2 4 3 3 3 2 4 3 2 ...
     $ satisf_i  : int  2 3 5 4 3 4 2 4 4 3 ...
     $ satisf_al : int  2 3 4 4 3 4 3 4 4 4 ...
     $ repurchase: int  2 4 4 4 3 3 4 4 4 4 ...



```python
table(a$gender)
```



      1   2
    132 115



```python
table(a$marriage)
```



      1   2
     71 176



```python
attach(a) # $사용안해도 됨
```


```python
table(gender)
```


    gender
      1   2
    132 115



```python
table(job)
```


    job
     1  2  3  4  5  6  7  8  9 10
    13 23 39 89  8 19 27 14  6  9



```python
detach(a)
```


```python
a$gender<-factor(a$gender, levels=c(1,2), labels = c('male','female'))
```


```python
str(a)
```

    'data.frame':	247 obs. of  17 variables:
     $ gender    : Factor w/ 2 levels "male","female": 1 2 2 2 1 2 2 1 2 2 ...
     $ marriage  : int  1 1 2 2 2 2 1 1 2 2 ...
     $ edu       : int  4 4 4 4 6 2 6 6 4 4 ...
     $ job       : int  1 9 4 7 6 7 4 4 5 5 ...
     $ mincome   : int  2 2 3 5 5 3 5 5 2 2 ...
     $ aware     : int  2 1 1 2 2 1 1 4 2 1 ...
     $ count     : int  1 4 6 6 2 2 5 10 2 2 ...
     $ amount    : int  11000 30000 100000 65000 50000 100000 100000 39000 40000 100000 ...
     $ decision  : int  2 1 3 3 2 2 3 3 3 3 ...
     $ propensity: int  1 1 2 2 2 1 2 2 2 3 ...
     $ skin      : int  1 3 3 5 3 4 5 2 3 3 ...
     $ promo     : int  1 2 2 2 2 2 2 1 2 1 ...
     $ location  : int  2 3 2 3 3 3 3 2 3 3 ...
     $ satisf_b  : int  5 2 4 3 3 3 2 4 3 2 ...
     $ satisf_i  : int  2 3 5 4 3 4 2 4 4 3 ...
     $ satisf_al : int  2 3 4 4 3 4 3 4 4 4 ...
     $ repurchase: int  2 4 4 4 3 3 4 4 4 4 ...



```python
table(a$gender)
```



      male female
       132    115



```python
install.packages('descr')
```

    package 'descr' successfully unpacked and MD5 sums checked

    The downloaded binary packages are in
    	C:\Users\MyCom\AppData\Local\Temp\Rtmp8OPV9d\downloaded_packages



```python
library(descr)
```

    Warning message:
    "package 'descr' was built under R version 3.6.3"


```python
freq(a$gender) #freq 사용하려면 descr 라이브러리 설치해줘야 함
```


<table>
<thead><tr><th></th><th scope=col>Frequency</th><th scope=col>Percent</th></tr></thead>
<tbody>
	<tr><th scope=row>male</th><td>132     </td><td> 53.4413</td></tr>
	<tr><th scope=row>female</th><td>115     </td><td> 46.5587</td></tr>
	<tr><th scope=row>Total</th><td>247     </td><td>100.0000</td></tr>
</tbody>
</table>





![output_18_1]({{ site.gdrive_url_prefix }}1IRTXI6-NfP-7cMu3xWCDtvtvcR5vbSuB)





```python
install.packages('ggplot2')
library(ggplot2)
```


      There is a binary version available but the source version is later:
            binary source needs_compilation
    ggplot2  3.3.3  3.3.5             FALSE



    installing the source package 'ggplot2'

    Registered S3 methods overwritten by 'tibble':
      method     from
      format.tbl pillar
      print.tbl  pillar



```python
ggplot(a,aes(x=gender)) + geom_bar(color='blue')
```

![output_20_0]({{ site.gdrive_url_prefix }}1N_ye88QCYK2nafr1H93pT9vg9N08xtBM)






```python
attach(a)
```


```python
freq(edu)
```


<table>
<thead><tr><th></th><th scope=col>Frequency</th><th scope=col>Percent</th></tr></thead>
<tbody>
	<tr><th scope=row>2</th><td> 30        </td><td> 12.1457490</td></tr>
	<tr><th scope=row>3</th><td>  9        </td><td>  3.6437247</td></tr>
	<tr><th scope=row>4</th><td>136        </td><td> 55.0607287</td></tr>
	<tr><th scope=row>5</th><td>  2        </td><td>  0.8097166</td></tr>
	<tr><th scope=row>6</th><td> 29        </td><td> 11.7408907</td></tr>
	<tr><th scope=row>7</th><td> 15        </td><td>  6.0728745</td></tr>
	<tr><th scope=row>8</th><td> 26        </td><td> 10.5263158</td></tr>
	<tr><th scope=row>Total</th><td>247        </td><td>100.0000000</td></tr>
</tbody>
</table>



![output_22_1]({{ site.gdrive_url_prefix }}1qeV3OyO9pAAm1SGTm-e4twmouPkKTizL)





```python
install.packages('car')
library(car)
```

    also installing the dependency 'lme4'




      There are binary versions available but the source versions are later:
         binary   source needs_compilation
    lme4 1.1-26 1.1-27.1              TRUE
    car  3.0-10   3.0-12             FALSE

      Binaries will be installed
    package 'lme4' successfully unpacked and MD5 sums checked

    The downloaded binary packages are in
    	C:\Users\MyCom\AppData\Local\Temp\Rtmp8OPV9d\downloaded_packages


    installing the source package 'car'

    Loading required package: carData



```python
a$eduM<-recode(edu,"lo:2=1; 3:4=2; 5:hi=3; else='NA'") # edu 컬럼 그대로 두고 eduM 컬럼 만들어 준다. 2를 1로 변경 , 3~4를 2로 변경 해준다.


```


```python
freq(a$eduM) #3집단으로 묶인것을 확인가능
```


<table>
<thead><tr><th></th><th scope=col>Frequency</th><th scope=col>Percent</th></tr></thead>
<tbody>
	<tr><th scope=row>1</th><td> 30      </td><td> 12.14575</td></tr>
	<tr><th scope=row>2</th><td>145      </td><td> 58.70445</td></tr>
	<tr><th scope=row>3</th><td> 72      </td><td> 29.14980</td></tr>
	<tr><th scope=row>Total</th><td>247      </td><td>100.00000</td></tr>
</tbody>
</table>




![output_25_1]({{ site.gdrive_url_prefix }}1Z82r5Yw6KxWJKxnnWrAatp1A3rgoHcdY)




```python
a$eduM <- factor(a$eduM, levels=c(1,2,3), labels=c('중졸이하','고졸이하','대졸이상'))
```


```python
freq(a$eduM)
```


<table>
<thead><tr><th></th><th scope=col>Frequency</th><th scope=col>Percent</th></tr></thead>
<tbody>
	<tr><th scope=row>중졸이하</th><td> 30      </td><td> 12.14575</td></tr>
	<tr><th scope=row>고졸이하</th><td>145      </td><td> 58.70445</td></tr>
	<tr><th scope=row>대졸이상</th><td> 72      </td><td> 29.14980</td></tr>
	<tr><th scope=row>Total</th><td>247      </td><td>100.00000</td></tr>
</tbody>
</table>



![output_27_1]({{ site.gdrive_url_prefix }}1No6UDb8BBkN-56_Cwnp154G-xnN9qVIH)




# 기술 통계

1. 중심화경향

- 평균 , 중위수 , 최빈값(Mode) => 중심은 어디인가?


2. 산포도

- 분산 : 편차의 제곱의 평균

- 표준편차 : 분산에 루트를 씌운 값

- 범위

- 사분위범위

3. 분포도

- 왜도 : 좌우대칭정도

- 첨도 : 뾰족함 정도


```python
attach(a)
```

    The following objects are masked from a (pos = 5):

        amount, aware, count, decision, edu, gender, job, location,
        marriage, mincome, promo, propensity, repurchase, satisf_al,
        satisf_b, satisf_i, skin




```python
max(amount)
```


5000000



```python
min(amount)
```


3000



```python
sum(amount)
```


38023000



```python
mean(amount)
```


153939.271255061



```python
var(amount)
```


158463699549.06



```python
sd(amount)
```


398074.992368348



```python
install.packages('psych')
```

    also installing the dependencies 'tmvnsim', 'mnormt'




      There is a binary version available but the source version is later:
          binary source needs_compilation
    psych  2.1.3  2.1.9             FALSE

    package 'tmvnsim' successfully unpacked and MD5 sums checked
    package 'mnormt' successfully unpacked and MD5 sums checked

    The downloaded binary packages are in
    	C:\Users\MyCom\AppData\Local\Temp\Rtmp8OPV9d\downloaded_packages


    installing the source package 'psych'




```python
library(psych)
```


    Attaching package: 'psych'

    The following object is masked from 'package:car':

        logit

    The following objects are masked from 'package:ggplot2':

        %+%, alpha




```python
describe(a)
```


<table>
<thead><tr><th></th><th scope=col>vars</th><th scope=col>n</th><th scope=col>mean</th><th scope=col>sd</th><th scope=col>median</th><th scope=col>trimmed</th><th scope=col>mad</th><th scope=col>min</th><th scope=col>max</th><th scope=col>range</th><th scope=col>skew</th><th scope=col>kurtosis</th><th scope=col>se</th></tr></thead>
<tbody>
	<tr><th scope=row>gender*</th><td> 1          </td><td>247         </td><td>1.465587e+00</td><td>4.998272e-01</td><td>    1       </td><td>    1.457286</td><td>    0.0000  </td><td>   1        </td><td>      2     </td><td>      1     </td><td> 0.13714193 </td><td>-1.9891964  </td><td>3.180324e-02</td></tr>
	<tr><th scope=row>marriage</th><td> 2          </td><td>247         </td><td>1.712551e+00</td><td>4.534918e-01</td><td>    2       </td><td>    1.763819</td><td>    0.0000  </td><td>   1        </td><td>      2     </td><td>      1     </td><td>-0.93360038 </td><td>-1.1329280  </td><td>2.885499e-02</td></tr>
	<tr><th scope=row>edu</th><td> 3          </td><td>247         </td><td>4.566802e+00</td><td>1.709191e+00</td><td>    4       </td><td>    4.462312</td><td>    0.0000  </td><td>   2        </td><td>      8     </td><td>      6     </td><td> 0.63815084 </td><td>-0.3287273  </td><td>1.087532e-01</td></tr>
	<tr><th scope=row>job</th><td> 4          </td><td>247         </td><td>4.578947e+00</td><td>2.199603e+00</td><td>    4       </td><td>    4.422111</td><td>    1.4826  </td><td>   1        </td><td>     10     </td><td>      9     </td><td> 0.68090959 </td><td>-0.1651951  </td><td>1.399574e-01</td></tr>
	<tr><th scope=row>mincome</th><td> 5          </td><td>247         </td><td>3.757085e+00</td><td>1.674079e+00</td><td>    4       </td><td>    3.819095</td><td>    1.4826  </td><td>   1        </td><td>      6     </td><td>      5     </td><td>-0.10186401 </td><td>-1.2266191  </td><td>1.065191e-01</td></tr>
	<tr><th scope=row>aware</th><td> 6          </td><td>247         </td><td>3.319838e+00</td><td>5.575692e+00</td><td>    2       </td><td>    1.924623</td><td>    0.0000  </td><td>   1        </td><td>     31     </td><td>     30     </td><td> 3.98663626 </td><td>15.7864331  </td><td>3.547728e-01</td></tr>
	<tr><th scope=row>count</th><td> 7          </td><td>247         </td><td>4.327935e+00</td><td>4.422061e+00</td><td>    3       </td><td>    3.492462</td><td>    2.9652  </td><td>   1        </td><td>     36     </td><td>     35     </td><td> 3.08793674 </td><td>13.5854742  </td><td>2.813690e-01</td></tr>
	<tr><th scope=row>amount</th><td> 8          </td><td>247         </td><td>1.539393e+05</td><td>3.980750e+05</td><td>52000       </td><td>83798.994975</td><td>47443.2000  </td><td>3000        </td><td>5000000     </td><td>4997000     </td><td> 8.62153257 </td><td>92.2401960  </td><td>2.532891e+04</td></tr>
	<tr><th scope=row>decision</th><td> 9          </td><td>247         </td><td>2.388664e+00</td><td>7.615994e-01</td><td>    3       </td><td>    2.482412</td><td>    0.0000  </td><td>   1        </td><td>      3     </td><td>      2     </td><td>-0.77786381 </td><td>-0.8701841  </td><td>4.845941e-02</td></tr>
	<tr><th scope=row>propensity</th><td>10          </td><td>247         </td><td>1.975709e+00</td><td>6.803103e-01</td><td>    2       </td><td>    1.969849</td><td>    0.0000  </td><td>   1        </td><td>      3     </td><td>      2     </td><td> 0.02958183 </td><td>-0.8487310  </td><td>4.328711e-02</td></tr>
	<tr><th scope=row>skin</th><td>11          </td><td>247         </td><td>2.761134e+00</td><td>1.488311e+00</td><td>    3       </td><td>    2.703518</td><td>    2.9652  </td><td>   1        </td><td>      5     </td><td>      4     </td><td> 0.15331957 </td><td>-1.3373908  </td><td>9.469894e-02</td></tr>
	<tr><th scope=row>promo</th><td>12          </td><td>247         </td><td>2.016194e+00</td><td>8.212998e-01</td><td>    2       </td><td>    1.919598</td><td>    0.0000  </td><td>   1        </td><td>      4     </td><td>      3     </td><td> 0.84726856 </td><td> 0.5434697  </td><td>5.225806e-02</td></tr>
	<tr><th scope=row>location</th><td>13          </td><td>247         </td><td>2.465587e+00</td><td>1.073437e+00</td><td>    3       </td><td>    2.371859</td><td>    1.4826  </td><td>   1        </td><td>      5     </td><td>      4     </td><td> 0.55038656 </td><td> 0.1943319  </td><td>6.830114e-02</td></tr>
	<tr><th scope=row>satisf_b</th><td>14          </td><td>247         </td><td>2.890688e+00</td><td>7.809953e-01</td><td>    3       </td><td>    2.869347</td><td>    0.0000  </td><td>   1        </td><td>      5     </td><td>      4     </td><td> 0.14047539 </td><td> 0.3237416  </td><td>4.969354e-02</td></tr>
	<tr><th scope=row>satisf_i</th><td>15          </td><td>247         </td><td>3.404858e+00</td><td>8.301096e-01</td><td>    3       </td><td>    3.482412</td><td>    1.4826  </td><td>   1        </td><td>      5     </td><td>      4     </td><td>-0.69559430 </td><td> 0.9204758  </td><td>5.281861e-02</td></tr>
	<tr><th scope=row>satisf_al</th><td>16          </td><td>247         </td><td>3.461538e+00</td><td>7.527311e-01</td><td>    4       </td><td>    3.512563</td><td>    1.4826  </td><td>   1        </td><td>      5     </td><td>      4     </td><td>-0.98037384 </td><td> 2.1617488  </td><td>4.789513e-02</td></tr>
	<tr><th scope=row>repurchase</th><td>17          </td><td>247         </td><td>3.554656e+00</td><td>7.241820e-01</td><td>    4       </td><td>    3.633166</td><td>    0.0000  </td><td>   1        </td><td>      5     </td><td>      4     </td><td>-1.27727971 </td><td> 2.5541785  </td><td>4.607860e-02</td></tr>
	<tr><th scope=row>eduM*</th><td>18          </td><td>247         </td><td>2.170040e+00</td><td>6.209693e-01</td><td>    2       </td><td>    2.211055</td><td>    0.0000  </td><td>   1        </td><td>      3     </td><td>      2     </td><td>-0.12856235 </td><td>-0.5355836  </td><td>3.951133e-02</td></tr>
</tbody>
</table>




```python
summary(amount)
```


       Min. 1st Qu.  Median    Mean 3rd Qu.    Max.
       3000   30000   52000  153939  100000 5000000



```python
aggregate(amount~gender, a, mean)
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>amount</th></tr></thead>
<tbody>
	<tr><td>male    </td><td>127757.6</td></tr>
	<tr><td>female  </td><td>183991.3</td></tr>
</tbody>
</table>




```python
tapply(amount,gender, mean)
```


<dl class=dl-horizontal>
	<dt>male</dt>
		<dd>127757.575757576</dd>
	<dt>female</dt>
		<dd>183991.304347826</dd>
</dl>




```python
hist(amount)
```


![output_42_0]({{ site.gdrive_url_prefix }}1eVoaKPwt_EkSNWa1x6NxzxJVqMxX1wic)




```python
boxplot(amount) #이상치 판단하기
```


![output_43_0]({{ site.gdrive_url_prefix }}1CjmMjYH4XA7TvMgRJ5D2mskglqiCL9ui)




```python
qplot(amount,geom='histogram')
```

    `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.



![output_44_1]({{ site.gdrive_url_prefix }}1mHNeDKN_ndXAdDF3BPeK1VnxZW0BimGn)


```python
qplot(amount,geom='histogram',bins=50)
```


![output_45_0]({{ site.gdrive_url_prefix }}1uGffFauVHWLgFVQtT8_WeRkrIyhlTf58)




```python
qplot(amount,geom='histogram',bandwidth=50)
```

    Warning message:
    "Ignoring unknown parameters: bandwidth"`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.


![output_46_1]({{ site.gdrive_url_prefix }}17IpQzbVax1HA1snmzb4JTwDGVgL-aZW0)





```python
qplot(amount,geom='histogram',main='Histogram for amount')
```

    `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.



![output_47_1]({{ site.gdrive_url_prefix }}1AT4QgIV8J0QBURMWgqNFwGKoUx_R7rUo)



# 이상치 제거

## IQR 기준 이상치 탐색 및 제거

### 이상치: Q1 값에서 IQR의 1.5배를 뺀 값보다 작은 값, Q3 값에서 IQR의 1.5배를 더한값보다 큰 값 이다 .



![dd]({{ site.gdrive_url_prefix }}143_jROay9-npfYefZGkZAgVT-bQtQGj1)


```python
upQuan <- quantile(amount)[4]
```


```python
loQuan <- quantile(amount)[2]
```


```python
IQR=upQuan-loQuan
```


```python
IQR
```


<strong>75%:</strong> 70000



```python
a$amount<-ifelse(amount>upQuan+IQR*1.5 | amount <loQuan-IQR*1.5, NA, a$amount)
```


```python
describe(a) # amount만 217이므로 이상치 제거해줘야 한다
# 이상치 제거후 71119.815668 값 줄어든것을 확인할 수 있다.
```


<table>
<thead><tr><th></th><th scope=col>vars</th><th scope=col>n</th><th scope=col>mean</th><th scope=col>sd</th><th scope=col>median</th><th scope=col>trimmed</th><th scope=col>mad</th><th scope=col>min</th><th scope=col>max</th><th scope=col>range</th><th scope=col>skew</th><th scope=col>kurtosis</th><th scope=col>se</th></tr></thead>
<tbody>
	<tr><th scope=row>gender*</th><td> 1          </td><td>247         </td><td>    1.465587</td><td>4.998272e-01</td><td>    1       </td><td>    1.457286</td><td>    0.0000  </td><td>   1        </td><td>     2      </td><td>     1      </td><td> 0.13714193 </td><td>-1.9891964  </td><td>3.180324e-02</td></tr>
	<tr><th scope=row>marriage</th><td> 2          </td><td>247         </td><td>    1.712551</td><td>4.534918e-01</td><td>    2       </td><td>    1.763819</td><td>    0.0000  </td><td>   1        </td><td>     2      </td><td>     1      </td><td>-0.93360038 </td><td>-1.1329280  </td><td>2.885499e-02</td></tr>
	<tr><th scope=row>edu</th><td> 3          </td><td>247         </td><td>    4.566802</td><td>1.709191e+00</td><td>    4       </td><td>    4.462312</td><td>    0.0000  </td><td>   2        </td><td>     8      </td><td>     6      </td><td> 0.63815084 </td><td>-0.3287273  </td><td>1.087532e-01</td></tr>
	<tr><th scope=row>job</th><td> 4          </td><td>247         </td><td>    4.578947</td><td>2.199603e+00</td><td>    4       </td><td>    4.422111</td><td>    1.4826  </td><td>   1        </td><td>    10      </td><td>     9      </td><td> 0.68090959 </td><td>-0.1651951  </td><td>1.399574e-01</td></tr>
	<tr><th scope=row>mincome</th><td> 5          </td><td>247         </td><td>    3.757085</td><td>1.674079e+00</td><td>    4       </td><td>    3.819095</td><td>    1.4826  </td><td>   1        </td><td>     6      </td><td>     5      </td><td>-0.10186401 </td><td>-1.2266191  </td><td>1.065191e-01</td></tr>
	<tr><th scope=row>aware</th><td> 6          </td><td>247         </td><td>    3.319838</td><td>5.575692e+00</td><td>    2       </td><td>    1.924623</td><td>    0.0000  </td><td>   1        </td><td>    31      </td><td>    30      </td><td> 3.98663626 </td><td>15.7864331  </td><td>3.547728e-01</td></tr>
	<tr><th scope=row>count</th><td> 7          </td><td>247         </td><td>    4.327935</td><td>4.422061e+00</td><td>    3       </td><td>    3.492462</td><td>    2.9652  </td><td>   1        </td><td>    36      </td><td>    35      </td><td> 3.08793674 </td><td>13.5854742  </td><td>2.813690e-01</td></tr>
	<tr><th scope=row>amount</th><td> 8          </td><td>217         </td><td>71119.815668</td><td>5.355038e+04</td><td>50000       </td><td>63062.857143</td><td>44478.0000  </td><td>3000        </td><td>200000      </td><td>197000      </td><td> 1.16454289 </td><td> 0.5555427  </td><td>3.635237e+03</td></tr>
	<tr><th scope=row>decision</th><td> 9          </td><td>247         </td><td>    2.388664</td><td>7.615994e-01</td><td>    3       </td><td>    2.482412</td><td>    0.0000  </td><td>   1        </td><td>     3      </td><td>     2      </td><td>-0.77786381 </td><td>-0.8701841  </td><td>4.845941e-02</td></tr>
	<tr><th scope=row>propensity</th><td>10          </td><td>247         </td><td>    1.975709</td><td>6.803103e-01</td><td>    2       </td><td>    1.969849</td><td>    0.0000  </td><td>   1        </td><td>     3      </td><td>     2      </td><td> 0.02958183 </td><td>-0.8487310  </td><td>4.328711e-02</td></tr>
	<tr><th scope=row>skin</th><td>11          </td><td>247         </td><td>    2.761134</td><td>1.488311e+00</td><td>    3       </td><td>    2.703518</td><td>    2.9652  </td><td>   1        </td><td>     5      </td><td>     4      </td><td> 0.15331957 </td><td>-1.3373908  </td><td>9.469894e-02</td></tr>
	<tr><th scope=row>promo</th><td>12          </td><td>247         </td><td>    2.016194</td><td>8.212998e-01</td><td>    2       </td><td>    1.919598</td><td>    0.0000  </td><td>   1        </td><td>     4      </td><td>     3      </td><td> 0.84726856 </td><td> 0.5434697  </td><td>5.225806e-02</td></tr>
	<tr><th scope=row>location</th><td>13          </td><td>247         </td><td>    2.465587</td><td>1.073437e+00</td><td>    3       </td><td>    2.371859</td><td>    1.4826  </td><td>   1        </td><td>     5      </td><td>     4      </td><td> 0.55038656 </td><td> 0.1943319  </td><td>6.830114e-02</td></tr>
	<tr><th scope=row>satisf_b</th><td>14          </td><td>247         </td><td>    2.890688</td><td>7.809953e-01</td><td>    3       </td><td>    2.869347</td><td>    0.0000  </td><td>   1        </td><td>     5      </td><td>     4      </td><td> 0.14047539 </td><td> 0.3237416  </td><td>4.969354e-02</td></tr>
	<tr><th scope=row>satisf_i</th><td>15          </td><td>247         </td><td>    3.404858</td><td>8.301096e-01</td><td>    3       </td><td>    3.482412</td><td>    1.4826  </td><td>   1        </td><td>     5      </td><td>     4      </td><td>-0.69559430 </td><td> 0.9204758  </td><td>5.281861e-02</td></tr>
	<tr><th scope=row>satisf_al</th><td>16          </td><td>247         </td><td>    3.461538</td><td>7.527311e-01</td><td>    4       </td><td>    3.512563</td><td>    1.4826  </td><td>   1        </td><td>     5      </td><td>     4      </td><td>-0.98037384 </td><td> 2.1617488  </td><td>4.789513e-02</td></tr>
	<tr><th scope=row>repurchase</th><td>17          </td><td>247         </td><td>    3.554656</td><td>7.241820e-01</td><td>    4       </td><td>    3.633166</td><td>    0.0000  </td><td>   1        </td><td>     5      </td><td>     4      </td><td>-1.27727971 </td><td> 2.5541785  </td><td>4.607860e-02</td></tr>
	<tr><th scope=row>eduM*</th><td>18          </td><td>247         </td><td>    2.170040</td><td>6.209693e-01</td><td>    2       </td><td>    2.211055</td><td>    0.0000  </td><td>   1        </td><td>     3      </td><td>     2      </td><td>-0.12856235 </td><td>-0.5355836  </td><td>3.951133e-02</td></tr>
</tbody>
</table>




```python
a$amount<-ifelse(amount>100000, NA, a$amount)
```


```python
describe(a)
```


<table>
<thead><tr><th></th><th scope=col>vars</th><th scope=col>n</th><th scope=col>mean</th><th scope=col>sd</th><th scope=col>median</th><th scope=col>trimmed</th><th scope=col>mad</th><th scope=col>min</th><th scope=col>max</th><th scope=col>range</th><th scope=col>skew</th><th scope=col>kurtosis</th><th scope=col>se</th></tr></thead>
<tbody>
	<tr><th scope=row>gender*</th><td> 1          </td><td>247         </td><td>    1.465587</td><td>4.998272e-01</td><td>    1       </td><td>    1.457286</td><td>    0.0000  </td><td>   1        </td><td>     2      </td><td>    1       </td><td> 0.13714193 </td><td>-1.9891964  </td><td>3.180324e-02</td></tr>
	<tr><th scope=row>marriage</th><td> 2          </td><td>247         </td><td>    1.712551</td><td>4.534918e-01</td><td>    2       </td><td>    1.763819</td><td>    0.0000  </td><td>   1        </td><td>     2      </td><td>    1       </td><td>-0.93360038 </td><td>-1.1329280  </td><td>2.885499e-02</td></tr>
	<tr><th scope=row>edu</th><td> 3          </td><td>247         </td><td>    4.566802</td><td>1.709191e+00</td><td>    4       </td><td>    4.462312</td><td>    0.0000  </td><td>   2        </td><td>     8      </td><td>    6       </td><td> 0.63815084 </td><td>-0.3287273  </td><td>1.087532e-01</td></tr>
	<tr><th scope=row>job</th><td> 4          </td><td>247         </td><td>    4.578947</td><td>2.199603e+00</td><td>    4       </td><td>    4.422111</td><td>    1.4826  </td><td>   1        </td><td>    10      </td><td>    9       </td><td> 0.68090959 </td><td>-0.1651951  </td><td>1.399574e-01</td></tr>
	<tr><th scope=row>mincome</th><td> 5          </td><td>247         </td><td>    3.757085</td><td>1.674079e+00</td><td>    4       </td><td>    3.819095</td><td>    1.4826  </td><td>   1        </td><td>     6      </td><td>    5       </td><td>-0.10186401 </td><td>-1.2266191  </td><td>1.065191e-01</td></tr>
	<tr><th scope=row>aware</th><td> 6          </td><td>247         </td><td>    3.319838</td><td>5.575692e+00</td><td>    2       </td><td>    1.924623</td><td>    0.0000  </td><td>   1        </td><td>    31      </td><td>   30       </td><td> 3.98663626 </td><td>15.7864331  </td><td>3.547728e-01</td></tr>
	<tr><th scope=row>count</th><td> 7          </td><td>247         </td><td>    4.327935</td><td>4.422061e+00</td><td>    3       </td><td>    3.492462</td><td>    2.9652  </td><td>   1        </td><td>    36      </td><td>   35       </td><td> 3.08793674 </td><td>13.5854742  </td><td>2.813690e-01</td></tr>
	<tr><th scope=row>amount</th><td> 8          </td><td>186         </td><td>53134.408602</td><td>3.040054e+04</td><td>50000       </td><td>52640.000000</td><td>29652.0000  </td><td>3000        </td><td>100000      </td><td>97000       </td><td> 0.39814918 </td><td>-1.1001380  </td><td>2.229076e+03</td></tr>
	<tr><th scope=row>decision</th><td> 9          </td><td>247         </td><td>    2.388664</td><td>7.615994e-01</td><td>    3       </td><td>    2.482412</td><td>    0.0000  </td><td>   1        </td><td>     3      </td><td>    2       </td><td>-0.77786381 </td><td>-0.8701841  </td><td>4.845941e-02</td></tr>
	<tr><th scope=row>propensity</th><td>10          </td><td>247         </td><td>    1.975709</td><td>6.803103e-01</td><td>    2       </td><td>    1.969849</td><td>    0.0000  </td><td>   1        </td><td>     3      </td><td>    2       </td><td> 0.02958183 </td><td>-0.8487310  </td><td>4.328711e-02</td></tr>
	<tr><th scope=row>skin</th><td>11          </td><td>247         </td><td>    2.761134</td><td>1.488311e+00</td><td>    3       </td><td>    2.703518</td><td>    2.9652  </td><td>   1        </td><td>     5      </td><td>    4       </td><td> 0.15331957 </td><td>-1.3373908  </td><td>9.469894e-02</td></tr>
	<tr><th scope=row>promo</th><td>12          </td><td>247         </td><td>    2.016194</td><td>8.212998e-01</td><td>    2       </td><td>    1.919598</td><td>    0.0000  </td><td>   1        </td><td>     4      </td><td>    3       </td><td> 0.84726856 </td><td> 0.5434697  </td><td>5.225806e-02</td></tr>
	<tr><th scope=row>location</th><td>13          </td><td>247         </td><td>    2.465587</td><td>1.073437e+00</td><td>    3       </td><td>    2.371859</td><td>    1.4826  </td><td>   1        </td><td>     5      </td><td>    4       </td><td> 0.55038656 </td><td> 0.1943319  </td><td>6.830114e-02</td></tr>
	<tr><th scope=row>satisf_b</th><td>14          </td><td>247         </td><td>    2.890688</td><td>7.809953e-01</td><td>    3       </td><td>    2.869347</td><td>    0.0000  </td><td>   1        </td><td>     5      </td><td>    4       </td><td> 0.14047539 </td><td> 0.3237416  </td><td>4.969354e-02</td></tr>
	<tr><th scope=row>satisf_i</th><td>15          </td><td>247         </td><td>    3.404858</td><td>8.301096e-01</td><td>    3       </td><td>    3.482412</td><td>    1.4826  </td><td>   1        </td><td>     5      </td><td>    4       </td><td>-0.69559430 </td><td> 0.9204758  </td><td>5.281861e-02</td></tr>
	<tr><th scope=row>satisf_al</th><td>16          </td><td>247         </td><td>    3.461538</td><td>7.527311e-01</td><td>    4       </td><td>    3.512563</td><td>    1.4826  </td><td>   1        </td><td>     5      </td><td>    4       </td><td>-0.98037384 </td><td> 2.1617488  </td><td>4.789513e-02</td></tr>
	<tr><th scope=row>repurchase</th><td>17          </td><td>247         </td><td>    3.554656</td><td>7.241820e-01</td><td>    4       </td><td>    3.633166</td><td>    0.0000  </td><td>   1        </td><td>     5      </td><td>    4       </td><td>-1.27727971 </td><td> 2.5541785  </td><td>4.607860e-02</td></tr>
	<tr><th scope=row>eduM*</th><td>18          </td><td>247         </td><td>    2.170040</td><td>6.209693e-01</td><td>    2       </td><td>    2.211055</td><td>    0.0000  </td><td>   1        </td><td>     3      </td><td>    2       </td><td>-0.12856235 </td><td>-0.5355836  </td><td>3.951133e-02</td></tr>
</tbody>
</table>




```python
qplot(a$amount,geom='histogram',main='Histogram for amount')
```

    `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
    Warning message:
    "Removed 61 rows containing non-finite values (stat_bin)."


![output_58_1]({{ site.gdrive_url_prefix }}1ZOiomaKF5muMGtC9h_1aV_Lzq871Vvrd)



# 표준 오차 (S.E.)

## s/root n

### s : 동질성 ( 표준오차가 작다는 것은 그만큼 동질하다는 의미, 모집단도 동일하다는 의미)

### root n : 대표성( 표본이 많을수록 모집단의 특성을 더 많이 반영한다는 의미, 많은 표본이 표준오차가 적음)

- EX 1.

100명의  여자 대학생을 대상으로 데이트 상대 수를 구한 결과 평균 수가 4명이고 , 표준 편차 5라면, 표준오차는?

1. 표준오차  : 5/ ROOT 100 = 0.5

2. 의미 : 평균의 차이가 0.5이므로 3.5명~4.5명에 들 가능성이 높음

# 검정통계량

- 귀무가설과 현상(데이터)간에 차이를 계산한 값이 검정통계량 이다

- 카이제곱검정

![square]({{ site.gdrive_url_prefix }}1e7mdHztu4RlAey3soQ6m6kXSH_DxnTBo)


- 검정 통계량이 작다는 것은 귀무가설과 수집된 데이터 간의 차이가 작다는 것이다.


- 신뢰수준에 들 정도에 차이라면 귀무가설을 채택한다

- p-value(유의 확률, significance probability) 값이 0.05 미만이면 대립가설이 성립된다

- 검정통계량 = 차이/오차

- 검정통계량이 클수록 대립가설이 채택될 가능성이 높다

# 교차분석 실습


```python
a
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>marriage</th><th scope=col>edu</th><th scope=col>job</th><th scope=col>mincome</th><th scope=col>aware</th><th scope=col>count</th><th scope=col>amount</th><th scope=col>decision</th><th scope=col>propensity</th><th scope=col>skin</th><th scope=col>promo</th><th scope=col>location</th><th scope=col>satisf_b</th><th scope=col>satisf_i</th><th scope=col>satisf_al</th><th scope=col>repurchase</th><th scope=col>eduM</th></tr></thead>
<tbody>
	<tr><td>male    </td><td>1       </td><td>4       </td><td>1       </td><td>2       </td><td> 2      </td><td> 1      </td><td> 11000  </td><td>2       </td><td>1       </td><td>1       </td><td>1       </td><td>2       </td><td>5       </td><td>2       </td><td>2       </td><td>2       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>1       </td><td>4       </td><td>9       </td><td>2       </td><td> 1      </td><td> 4      </td><td> 30000  </td><td>1       </td><td>1       </td><td>3       </td><td>2       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td>4       </td><td>3       </td><td> 1      </td><td> 6      </td><td>100000  </td><td>3       </td><td>2       </td><td>3       </td><td>2       </td><td>2       </td><td>4       </td><td>5       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td>7       </td><td>5       </td><td> 2      </td><td> 6      </td><td> 65000  </td><td>3       </td><td>2       </td><td>5       </td><td>2       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>2       </td><td>6       </td><td>6       </td><td>5       </td><td> 2      </td><td> 2      </td><td> 50000  </td><td>2       </td><td>2       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>대졸이상</td></tr>
	<tr><td>female  </td><td>2       </td><td>2       </td><td>7       </td><td>3       </td><td> 1      </td><td> 2      </td><td>100000  </td><td>2       </td><td>1       </td><td>4       </td><td>2       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>3       </td><td>중졸이하</td></tr>
	<tr><td>female  </td><td>1       </td><td>6       </td><td>4       </td><td>5       </td><td> 1      </td><td> 5      </td><td>100000  </td><td>3       </td><td>2       </td><td>5       </td><td>2       </td><td>3       </td><td>2       </td><td>2       </td><td>3       </td><td>4       </td><td>대졸이상</td></tr>
	<tr><td>male    </td><td>1       </td><td>6       </td><td>4       </td><td>5       </td><td> 4      </td><td>10      </td><td> 39000  </td><td>3       </td><td>2       </td><td>2       </td><td>1       </td><td>2       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td><td>대졸이상</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td>5       </td><td>2       </td><td> 2      </td><td> 2      </td><td> 40000  </td><td>3       </td><td>2       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td>5       </td><td>2       </td><td> 1      </td><td> 2      </td><td>100000  </td><td>3       </td><td>3       </td><td>3       </td><td>1       </td><td>3       </td><td>2       </td><td>3       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>1       </td><td>7       </td><td>4       </td><td>3       </td><td>10      </td><td> 3      </td><td> 50000  </td><td>1       </td><td>3       </td><td>1       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>대졸이상</td></tr>
	<tr><td>male    </td><td>1       </td><td>2       </td><td>5       </td><td>3       </td><td> 2      </td><td> 1      </td><td> 30000  </td><td>3       </td><td>2       </td><td>3       </td><td>2       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>중졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td>4       </td><td>3       </td><td> 4      </td><td> 4      </td><td>    NA  </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>2       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td>4       </td><td>2       </td><td> 3      </td><td> 2      </td><td>    NA  </td><td>1       </td><td>2       </td><td>3       </td><td>1       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>2       </td><td>4       </td><td>4       </td><td>6       </td><td> 2      </td><td> 2      </td><td> 60000  </td><td>3       </td><td>2       </td><td>1       </td><td>2       </td><td>5       </td><td>3       </td><td>3       </td><td>3       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>1       </td><td>4       </td><td>5       </td><td>2       </td><td> 2      </td><td> 3      </td><td> 50000  </td><td>1       </td><td>2       </td><td>4       </td><td>1       </td><td>3       </td><td>3       </td><td>4       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>2       </td><td>8       </td><td>3       </td><td>2       </td><td> 5      </td><td> 3      </td><td>    NA  </td><td>1       </td><td>3       </td><td>1       </td><td>2       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>대졸이상</td></tr>
	<tr><td>female  </td><td>1       </td><td>3       </td><td>8       </td><td>5       </td><td> 1      </td><td> 6      </td><td>    NA  </td><td>3       </td><td>3       </td><td>2       </td><td>4       </td><td>1       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>2       </td><td>2       </td><td>6       </td><td>2       </td><td> 4      </td><td> 1      </td><td> 80000  </td><td>2       </td><td>3       </td><td>1       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>중졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td>4       </td><td>3       </td><td> 8      </td><td> 3      </td><td> 30000  </td><td>2       </td><td>2       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>2       </td><td>4       </td><td>2       </td><td> 8      </td><td> 4      </td><td>    NA  </td><td>3       </td><td>2       </td><td>3       </td><td>2       </td><td>2       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>중졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td>7       </td><td>6       </td><td> 1      </td><td> 4      </td><td>    NA  </td><td>3       </td><td>3       </td><td>2       </td><td>2       </td><td>3       </td><td>2       </td><td>3       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td>7       </td><td>3       </td><td> 1      </td><td>25      </td><td> 50000  </td><td>2       </td><td>2       </td><td>1       </td><td>2       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>2       </td><td>9       </td><td>1       </td><td> 1      </td><td> 1      </td><td> 20000  </td><td>1       </td><td>1       </td><td>5       </td><td>1       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>중졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>3       </td><td>8       </td><td>4       </td><td> 2      </td><td> 3      </td><td> 42000  </td><td>1       </td><td>2       </td><td>3       </td><td>1       </td><td>3       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td>8       </td><td>4       </td><td> 2      </td><td> 3      </td><td> 42000  </td><td>3       </td><td>3       </td><td>2       </td><td>1       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>1       </td><td>4       </td><td>4       </td><td>3       </td><td> 2      </td><td>20      </td><td> 40000  </td><td>3       </td><td>1       </td><td>5       </td><td>2       </td><td>3       </td><td>2       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td>4       </td><td>6       </td><td> 1      </td><td> 6      </td><td> 70000  </td><td>3       </td><td>3       </td><td>5       </td><td>2       </td><td>1       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>8       </td><td>4       </td><td>5       </td><td> 5      </td><td> 6      </td><td>    NA  </td><td>2       </td><td>1       </td><td>4       </td><td>1       </td><td>1       </td><td>4       </td><td>4       </td><td>4       </td><td>3       </td><td>대졸이상</td></tr>
	<tr><td>male    </td><td>2       </td><td>4       </td><td>2       </td><td>6       </td><td> 2      </td><td> 1      </td><td>    NA  </td><td>3       </td><td>2       </td><td>1       </td><td>2       </td><td>2       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td> 5      </td><td>2       </td><td>1       </td><td>10      </td><td> 30000  </td><td>2       </td><td>2       </td><td>2       </td><td>4       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>2       </td><td>4       </td><td> 6      </td><td>6       </td><td>7       </td><td> 5      </td><td> 50000  </td><td>3       </td><td>2       </td><td>4       </td><td>2       </td><td>2       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>2       </td><td>4       </td><td> 1      </td><td>4       </td><td>1       </td><td> 1      </td><td> 10000  </td><td>1       </td><td>3       </td><td>1       </td><td>1       </td><td>1       </td><td>5       </td><td>1       </td><td>3       </td><td>1       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td> 4      </td><td>3       </td><td>2       </td><td> 1      </td><td> 10000  </td><td>1       </td><td>1       </td><td>4       </td><td>3       </td><td>2       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td> 4      </td><td>3       </td><td>2       </td><td> 3      </td><td> 50000  </td><td>3       </td><td>2       </td><td>5       </td><td>2       </td><td>3       </td><td>2       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>2       </td><td>2       </td><td> 5      </td><td>4       </td><td>2       </td><td> 1      </td><td> 60000  </td><td>1       </td><td>1       </td><td>1       </td><td>4       </td><td>5       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>중졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>6       </td><td> 1      </td><td>3       </td><td>2       </td><td> 2      </td><td> 50000  </td><td>3       </td><td>2       </td><td>5       </td><td>1       </td><td>2       </td><td>4       </td><td>4       </td><td>3       </td><td>4       </td><td>대졸이상</td></tr>
	<tr><td>female  </td><td>2       </td><td>6       </td><td> 4      </td><td>6       </td><td>1       </td><td> 3      </td><td>    NA  </td><td>3       </td><td>3       </td><td>5       </td><td>2       </td><td>1       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>대졸이상</td></tr>
	<tr><td>male    </td><td>2       </td><td>4       </td><td> 1      </td><td>3       </td><td>2       </td><td> 1      </td><td> 50000  </td><td>3       </td><td>2       </td><td>1       </td><td>2       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>6       </td><td> 3      </td><td>4       </td><td>1       </td><td> 1      </td><td>100000  </td><td>3       </td><td>2       </td><td>1       </td><td>2       </td><td>1       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>대졸이상</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td> 7      </td><td>4       </td><td>1       </td><td> 2      </td><td> 50000  </td><td>3       </td><td>1       </td><td>5       </td><td>2       </td><td>2       </td><td>3       </td><td>2       </td><td>3       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td> 4      </td><td>2       </td><td>2       </td><td>12      </td><td> 20000  </td><td>2       </td><td>2       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td> 4      </td><td>4       </td><td>2       </td><td> 4      </td><td> 35000  </td><td>2       </td><td>2       </td><td>1       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td> 4      </td><td>4       </td><td>2       </td><td> 4      </td><td> 30000  </td><td>2       </td><td>2       </td><td>1       </td><td>2       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td> 7      </td><td>1       </td><td>2       </td><td> 2      </td><td> 50000  </td><td>1       </td><td>1       </td><td>3       </td><td>2       </td><td>5       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td> 7      </td><td>1       </td><td>2       </td><td> 3      </td><td> 50000  </td><td>1       </td><td>2       </td><td>3       </td><td>2       </td><td>5       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>1       </td><td>4       </td><td> 4      </td><td>2       </td><td>2       </td><td> 7      </td><td> 80000  </td><td>3       </td><td>2       </td><td>3       </td><td>2       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td> 1      </td><td>3       </td><td>2       </td><td> 6      </td><td> 20000  </td><td>3       </td><td>1       </td><td>3       </td><td>1       </td><td>3       </td><td>2       </td><td>3       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td>10      </td><td>2       </td><td>2       </td><td> 2      </td><td> 25000  </td><td>3       </td><td>1       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>1       </td><td>3       </td><td> 8      </td><td>1       </td><td>1       </td><td> 7      </td><td>100000  </td><td>2       </td><td>1       </td><td>5       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>1       </td><td>2       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>2       </td><td>4       </td><td> 4      </td><td>3       </td><td>2       </td><td> 2      </td><td> 50000  </td><td>1       </td><td>2       </td><td>5       </td><td>2       </td><td>2       </td><td>3       </td><td>4       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>male    </td><td>2       </td><td>4       </td><td> 4      </td><td>5       </td><td>2       </td><td> 1      </td><td> 80000  </td><td>2       </td><td>2       </td><td>1       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>6       </td><td> 7      </td><td>5       </td><td>1       </td><td> 2      </td><td>    NA  </td><td>3       </td><td>3       </td><td>2       </td><td>3       </td><td>2       </td><td>3       </td><td>4       </td><td>3       </td><td>3       </td><td>대졸이상</td></tr>
	<tr><td>female  </td><td>2       </td><td>7       </td><td> 7      </td><td>4       </td><td>1       </td><td> 2      </td><td>    NA  </td><td>2       </td><td>2       </td><td>4       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>대졸이상</td></tr>
	<tr><td>male    </td><td>1       </td><td>2       </td><td> 1      </td><td>2       </td><td>2       </td><td> 5      </td><td>  3000  </td><td>1       </td><td>1       </td><td>2       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>중졸이하</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td> 2      </td><td>3       </td><td>2       </td><td> 6      </td><td>  4000  </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>4       </td><td>2       </td><td>1       </td><td>1       </td><td>1       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>4       </td><td> 4      </td><td>2       </td><td>1       </td><td>10      </td><td>    NA  </td><td>3       </td><td>2       </td><td>2       </td><td>1       </td><td>2       </td><td>3       </td><td>4       </td><td>4       </td><td>4       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>7       </td><td> 8      </td><td>1       </td><td>2       </td><td> 3      </td><td>100000  </td><td>1       </td><td>2       </td><td>1       </td><td>1       </td><td>5       </td><td>2       </td><td>5       </td><td>4       </td><td>4       </td><td>대졸이상</td></tr>
	<tr><td>male    </td><td>1       </td><td>4       </td><td> 6      </td><td>1       </td><td>3       </td><td> 2      </td><td> 20000  </td><td>3       </td><td>1       </td><td>1       </td><td>1       </td><td>3       </td><td>4       </td><td>3       </td><td>3       </td><td>2       </td><td>고졸이하</td></tr>
	<tr><td>female  </td><td>2       </td><td>6       </td><td>10      </td><td>1       </td><td>1       </td><td>10      </td><td>    NA  </td><td>3       </td><td>2       </td><td>3       </td><td>1       </td><td>3       </td><td>2       </td><td>3       </td><td>3       </td><td>3       </td><td>대졸이상</td></tr>
</tbody>
</table>




```python
table(propensity,skin) #구매성향 , 피부타입 ,비율알려면 액셀로 하기
```


              skin
    propensity  1  2  3  4  5
             1 22  1 12 11 14
             2 39  8 47 16 23
             3 20 11 10  4  9



```python
chisq.test(propensity, skin, correct = F) #검정통계량
```

    Warning message in chisq.test(propensity, skin, correct = F):
    "Chi-squared approximation may be incorrect"



    	Pearson's Chi-squared test

    data:  propensity and skin
    X-squared = 24.275, df = 8, p-value = 0.002061



# p-value = 0.002061 이므로 대립가설이 채택될 가능성이 높다.


```python
# 성별에 따라 인지하는 정도가 다른가??

table(gender,aware)
```


            aware
    gender    1  2  3  4  5  6  7  8 10 14 15 19 28 29 31
      male   18 83  3 10  3  0  1  2  0  3  2  0  1  0  6
      female 59 43  2  2  2  1  0  2  1  1  0  1  0  1  0



```python
chisq.test(gender,aware,correct = F)
```

    Warning message in chisq.test(gender, aware, correct = F):
    "Chi-squared approximation may be incorrect"



    	Pearson's Chi-squared test

    data:  gender and aware
    X-squared = 54.35, df = 14, p-value = 1.119e-06




```python
install.packages('gmodels')
```

    package 'gmodels' successfully unpacked and MD5 sums checked

    The downloaded binary packages are in
    	C:\Users\MyCom\AppData\Local\Temp\Rtmp8OPV9d\downloaded_packages



```python
library(gmodels)
```

    Warning message:
    "package 'gmodels' was built under R version 3.6.3"
    Attaching package: 'gmodels'

    The following object is masked from 'package:descr':

        CrossTable




```python
CrossTable(x=propensity, y= skin, chisq=T) #카이제곱 알수있음
```

    Warning message in chisq.test(t, correct = FALSE, ...):
    "Chi-squared approximation may be incorrect"



       Cell Contents
    |-------------------------|
    |                       N |
    | Chi-square contribution |
    |           N / Row Total |
    |           N / Col Total |
    |         N / Table Total |
    |-------------------------|


    Total Observations in Table:  247


                 | skin
      propensity |         1 |         2 |         3 |         4 |         5 | Row Total |
    -------------|-----------|-----------|-----------|-----------|-----------|-----------|
               1 |        22 |         1 |        12 |        11 |        14 |        60 |
                 |     0.274 |     3.064 |     1.352 |     1.599 |     0.715 |           |
                 |     0.367 |     0.017 |     0.200 |     0.183 |     0.233 |     0.243 |
                 |     0.272 |     0.050 |     0.174 |     0.355 |     0.304 |           |
                 |     0.089 |     0.004 |     0.049 |     0.045 |     0.057 |           |
    -------------|-----------|-----------|-----------|-----------|-----------|-----------|
               2 |        39 |         8 |        47 |        16 |        23 |       133 |
                 |     0.488 |     0.712 |     2.609 |     0.029 |     0.126 |           |
                 |     0.293 |     0.060 |     0.353 |     0.120 |     0.173 |     0.538 |
                 |     0.481 |     0.400 |     0.681 |     0.516 |     0.500 |           |
                 |     0.158 |     0.032 |     0.190 |     0.065 |     0.093 |           |
    -------------|-----------|-----------|-----------|-----------|-----------|-----------|
               3 |        20 |        11 |        10 |         4 |         9 |        54 |
                 |     0.297 |    10.046 |     1.714 |     1.138 |     0.111 |           |
                 |     0.370 |     0.204 |     0.185 |     0.074 |     0.167 |     0.219 |
                 |     0.247 |     0.550 |     0.145 |     0.129 |     0.196 |           |
                 |     0.081 |     0.045 |     0.040 |     0.016 |     0.036 |           |
    -------------|-----------|-----------|-----------|-----------|-----------|-----------|
    Column Total |        81 |        20 |        69 |        31 |        46 |       247 |
                 |     0.328 |     0.081 |     0.279 |     0.126 |     0.186 |           |
    -------------|-----------|-----------|-----------|-----------|-----------|-----------|


    Statistics for All Table Factors


    Pearson's Chi-squared test
    ------------------------------------------------------------
    Chi^2 =  24.27468     d.f. =  8     p =  0.002060876






```python
cross1<-table(propensity, skin)
```


```python
cross1
```


              skin
    propensity  1  2  3  4  5
             1 22  1 12 11 14
             2 39  8 47 16 23
             3 20 11 10  4  9



```python
barplot(as.matrix(cross1))
```



![output_80_0]({{ site.gdrive_url_prefix }}18Hs85b0GuRohs8xPQzq30vcEkqzucvjy)




```python

```
