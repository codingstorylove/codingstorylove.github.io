---
layout: single
title: "ARIMA 알고리즘 관련 논문 공부 및 정리"
date: "2022-03-04 17:30:12 +0900"
last_modified_at: "2022-03-04 17:30:12 +0900"
---

# 3. ARIMA 알고리즘 관련 논문 공부 및 정리


##  다중개입 계절형 ARIMA 모형을 이용한 KTX 수송수요 예측

- 출처 : [https://scienceon.kisti.re.kr/commons/util/originalView.do?cn=JAKO201912964890873&oCn=JAKO201912964890873&dbt=JAKO&journal=NJOU00025585&keyword=forecast%20](https://scienceon.kisti.re.kr/commons/util/originalView.do?cn=JAKO201912964890873&oCn=JAKO201912964890873&dbt=JAKO&journal=NJOU00025585&keyword=forecast%20)



> 1. 실제 데이터와 예측데이터 비교할때 참고

___________________________________________________
3.2. 모형검증

Table 3.1의 적합된 다중개입 계절형 ARIMA 모형의 예측력 검증을 위해 2016년 1월부터 2016년 11월
간의 예측치와 실측치를 제곱근평균제곱오차(root mean squared error; RMSE), 평균절대오차(mean
absolute error; MAE), 평균절대백분율 오차(mean absolute percent error; MAPE)로 비교하였다. 표
본구간 내 예측력 검정을 실시한 결과 MAPE 기준으로 경부축 주중과 주말은 각각 1.34%, 2.17%, 호
남축 주중과 주말은 각각 3.83%, 3.66%로 나타났다. 경부축에서는 RMSE, MAE, MAPE 모두 주중에
비해서는 주말이 더 크게 나타나고 있으며, 호남축에서는 RMSE, MAE는 주중이, MAPE는 주말이 더
크게 나타나고 있다. 경부축보다는 호남축의 수송수요가 안정이 덜 되어 있어 변동성이 상대적으로 크
기 때문에 예측오차가 높게 나타난 것으로 보인다.




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_154515_1.png")
```




    
![output_1_0]({{ site.gdrive_url_prefix }}1NMgi5lRoYG3fqQnURTjawpwPpwzsDWkS)

    



> 다중개입 시계열 모형 (ARIMA) 논문에 쓸때 참고


## ARIMA-Intervention 시계열 모형을 이용한 인천국제공항 식음료 매출 분석 및 추정 연구

- 출처 : [http://www.koreascience.or.kr/article/JAKO201913457809410.pdf](http://www.koreascience.or.kr/article/JAKO201913457809410.pdf)


1. 연구방법 -> 연구 설계

이를 위해 연구
자는 시계열 분석기법들 중 하나인 ARIMA-Intervention
(개입모형)을 이용하여 인천국제공항 식음료 매출액에
영향을 미칠 것으로 판단되는 주요 시계열 영향변수들을
구분하고 그에 따른 변화폭을 추정하였고 그 결과를 토
대로 향후 발생 가능할 것으로 예측되는 식음료 월별 매
출액을 추정하는 것을 목적으로 한 것이다

2. 연구설계 

2.2 ARIMA 시계열 연구모형
시차단위별로 구분되어 있는 시계열 데이터를 분석하여 그 관계를 모형으로 구성하고 이를 통해서 미래에 발생가능 한 수요 또는 자료를 예측하는 함수식으로는 크게 시계열모형(고전적 모형)과 확률적 시계열 모형 두가지로 구분할 수 있다. 

고전적 방법인 시계열 분석방법으로 이동평균법, 지수평활법 및 분해법 등이 있다. 

현대적 모형이라 할 수 있는 확률적 시계열 모형은 시계열
데이터(원자료)에 확률적(Stochastic) 방법론를 도입하여 모형화한 연구로서 ARIMA 분석법으로도 알려져 있다. 

ARIMA 분석모형은 A.R. (Auto Regression-자동회귀)와 M.A.(Moving Average -이동평균)가 결합하여 구
성된 분석모형을 의미한다[5]. 

- [5] M. S. Kim, K. W. Kim, S. S. Park, “A Study on the Air
Travel Demand Forecasting using Time Series
ARIMA-Intervention Model”, Journal of the Korean
Society for Aviation and Aeronautics, Vol. 20, No. 1,
pp.63-74, 2012

2.3 ARIMA 시계열-개입모형

항공수요 관련 시계열 데이터는 앞서 언급한 바와 같이 자연재해(지진, 쓰나미, 태풍, 중동호흡기증후군(메르스) 등), 전쟁 발발, 한한령 등 정치적 또는․경제적인 외부적 이벤트에 의해 큰 영향을 받는다. 

이와 같은 외부 이벤트를 ARIMA 연구모형에서는 개입변수 (intervention variable)이라고 부른다[11].

개입변수는 시계열 데이터의 정상적 흐름 및 확률적 구조에 급격한 변화를 주기 때문에 연구모형을 설계하는데 난제 역할을 한다. 

그러므로 이러한 외부적 충격요인을 연구모형에 변수로 포함시킬 수 있는 ARIMA 개입 모형을 활용하면 보다 미래 수요에 대한 예측력을 매우
향상시킬 수 있다[12][13]. 

ARIMA Intervention 모델은 외부적 충격을 변수로 포함하였다고 하여 ARIMA 확장 모형이라고 하였다.

원 자료인 시계열 데이터 Yt가 있고, 외부적 개입이 M번 발생했다고 한다면 ARIMA 개입모형은 다음과 같다


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_162439_1.png")
```



![output_5_0]({{ site.gdrive_url_prefix }}1VbsstD38j4gFt7GrLNZzY8fUePfVwhfv)

    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_162439_2.png")
```





![output_6_0]({{ site.gdrive_url_prefix }}1RTXJPWTOxJLp_ciKhwx-Iv6wgmspm9zp)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_162439_3.png")
```




![output_7_0]({{ site.gdrive_url_prefix }}1r2l0z8U2COYNQxnD-SGe72EhzADy0cdp)

    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_162439_4.png")
```




![output_8_0]({{ site.gdrive_url_prefix }}1cDF2BrWHu1ttIxyKu44bwujZ-BH7CH6s)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_162439_5.png")
```



![output_9_0]({{ site.gdrive_url_prefix }}1n4iuJWHM1PRjD1t0NL6y9ihDZDlGC6kg)

    



ARIMA-개입모형의 분석절차는 Box-Jenkins가 제안한 모형의 식별, 추정 및 검증 그리고 모형의 진단을 거쳐 최종적으로 선택된 모형을 이용해서 미래를 예측한다. 

식별(Identification) 단계에서 데이터의 순차도표, 자기상관함수(ACF), 편자기상관함수(PACF) 등을 통하여
데이터의 정상성 여부를 판단하였다. 

이를 위해 데이터의 차분이나, 로그 변환 여부를 판단 후 차분(d), AR(p),
MA(q) 차수들을 결정하였다. 

3.2 ARIMA 분석 : 식사(food)
    
ARIMA 분석에 앞서 먼저 식사 월별 매출액 데이터가 정상성이 있는지 여부를 식별하였다. 

2020년까지 미래의 매출액을 예측하기 위해서는 계절성(seasonality)과

같은 비정상성을 발견하여 제거하고 안정적이면서 고정적인 추세를 찾아내는 것이 필요하다.

> 계절성을 반영하지 않는 모델은 ARIMA 이라면 계절성 반영하는 모델은 SARIMA 이다.

- 출처: https://today-1.tistory.com/36 [Better Than,]

- SARIMA(Seasonal ARIMA )
 

: ARIMA 모형은 Non-seasonal 데이터 또는 Non-seasonal ARIMA 모델을 가정 -> 계절성 패턴 반영 모델 필요

: 계설정 패턴이 반영된 모델을 SARIMA(Seasonal ARIMA)라고 함

SARIMAX 클래스 이용하면 Multiplicated SARIMA(p,d,q)x(P,D,Q,m) 모형 추정 및 예측 가능
SARIMAX의 fit 메서드는 모수를 추정하여 그 결과를 SARIMAXResult 클래스 인스턴스로 반환





_________________________________

> Model fitness and statistics of food  => 모델 적합성 검사 및 모델 평가 할때 참고하기 좋음. 실제 데이터와 예측데이터 비교할때 참고


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_164249_1.png")
```



![output_15_0]({{ site.gdrive_url_prefix }}1DXQj0zJehKbJP2j4vAi2h1kCi1Y7AKDd)

    



식사 데이터를 토대로 추정한 ARIMA 개입모형의 적합도 분석 결과는 Table 2와 같다. 

예측 모형에서 자기상관함수의 잔차의 Ljung-Box 통계량의 p-value 값이 .961로 분석되어 95% 신뢰수준에서 유의하지 않다. 

즉,‘잔차 사이에 자기상관이 없고 잔차의 분포는 독립적(무
차별)이다’라는 귀무가설을 채택한다는 의미로 해석된다
[5, 8].

또한 추정된 ARIMA 개입모형의 예측치는 실측치와 예측오차 검토과정을 통해서 그 정확도를 측정할 수 있다. 

Table 2에 제시된 MAPE 적합도 계수를 활용하였다.

MAPE는 절대평균백분율오차(Mean Absolute Percentage
Error)를 의미한다. 

MAPE 적합도 계수는 아래와 같이 구간별로 적합도를 해석할 수 있다.


__________________________________________________________
> Ljung-Box Q 통계량이란?

- 출처 : [https://m.blog.naver.com/risk_girl/220834418182](https://m.blog.naver.com/risk_girl/220834418182)

일정 기간 동안 일련의 관측치가 랜덤이고 독립적인지 여부를 검사하는 데 사용

자기 상관 시계열 자료에서 시점 간의 상관 관계를 의미하며, 자기 상관 관계로 인하여 시계열 모형의 정확도를 떨어뜨리는 원인이 되기도 한다.

예를 들어, 한 전자제품 회사에서 5년 간에 걸친 배터리의 월간 판매량을 이용하여 시계열 분석 모형을 개발하려고 합니다. 그렇지만 월간 판매는 계절별 추세에 영향을 받을 수 있습니다. 예를 들어 매년 크리스마스에 사람들이 장난감용 배터리를 살 때 판매 증가가 발생합니다. 따라서 한 해의 월간 판매 관측치는 12개월 후(시차 12)의 월간 판매 관측치와 상관될 수 있습니다.

시계열 분석 모형을 선택하기 전에 월간 판매 차이에 대한 자기 상관 여부를 확인할 수 있습니다. 

Ljung-Box Q(LBQ) 통계량은 시차 k에 대한 자기 상관이 0이라는 귀무가설을 검증하기 위해 사용된다.
즉, 위의 예제의 경우 12에 대해 랜덤이고 독립적이라는 귀무 가설을 검정합니다.

귀무가설: 자기상관성이 없다. 상관계수=0

대립가설: 자기상관성이 있다. 상관계수≠0

해석 방법

LBQ가 지정된 임계값 보다 크면 즉 P-value가 기준이 되는 유의수준(0.05) 보다 작으면 귀무가설을 기각하게 되어 자기 상관성이 존재한다는 의미이다. 따라서 시계열 자료 간에는 일정 기간 랜덤 및 독립적이 아니라는 것을 의미한다.
LBQ는 또한 ARIMA 등 시계열 분석 모형을 적합한 후에, 잔차가 독립적임을 확인하기 위해 가정을 평가하는 데 사용됩니다.
Ljung-Box는 Portmanteau 검정이며, Box-Pierce 카이-제곱 통계량의 수정된 변형입니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_170226_1.png")

```



![output_18_0]({{ site.gdrive_url_prefix }}14XBv3fkxcSFLqavbaHIlk2sNv69PItLF)

    



Table 2에 제시된 바와 같이 실측치와 예측치 간 예
측오차를 비교한 MAPE 값이 5.467%로 나타나 우수한
예측력을 보이고 있는 것으로 판단할 수 있다[14][15].


또한 Fig 7과 같이 자기상관함수 및 편자기상관 함수의
잔차들이 95% 신뢰구간 내에 모두 있기 때문에 백색잡
음(White nosie) 조건을 만족하는 것으로 분석되었다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_171225_1.png")

```



![output_20_0]({{ site.gdrive_url_prefix }}1Zl76ULZ1Q4QaW-VeJSUHYsMiWjjGdj4a)

    
    



> 자기상관함수(AutoCovariance Function; ACF) 이란?



시계열 자료가 다른 자료, 대표적으로 횡단면 자료와 가장 큰 차이를 보이는 것은 바로 자료의 index가 시간(Time)이라는 것이고, 이로 인해 현재의 상태가 과거, 미래의 상태와 매우 밀접한 관련을 갖고 있다는 것입니다.

 

바로 시간의 흐름에 따라 독립적이지 않다는 것이죠.

오늘 주식의 가격이 어제의 가격에 영향을 받고, 내일의 가격에 영향을 주는 것처럼요.

 

이러한 경우 시계열 자료는 자기상관관계를 갖는다고 합니다.



- 출처: [https://datalabbit.tistory.com/112](https://datalabbit.tistory.com/112) [간토끼 DataMining Lab]


> 다음편에서 계속됩니다.

>  다른 알고리즘 참고할때 좋은 사이트

Probabilistic forecasting: DeepAR 

- DeepAR 공식 참고하기 좋은 사이트 

- 출처:[https://www.kaggle.com/yamqwe/probabilistic-forecasting-deepar](https://www.kaggle.com/yamqwe/probabilistic-forecasting-deepar)

>  논문 쓸때 좋은 데이터 

1.  amazon uk shoes dataset  - [https://data.world/crawlfeeds/amazon-uk-shoes-dataset](https://data.world/crawlfeeds/amazon-uk-shoes-dataset)
