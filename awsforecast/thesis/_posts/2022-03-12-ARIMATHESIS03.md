---
layout: single
title: "ARIMA 알고리즘 관련 논문 공부 및 정리 03"
date: "2022-03-12 19:44:11 +0900"
last_modified_at: "2022-03-12 19:44:11 +0900"
---

#  ARIMA 알고리즘 관련 논문 공부 및 정리 03

## 출처 -[https://pdfs.semanticscholar.org/c585/52826efc9c3e7f6722b9905057a41e772aeb.pdf](https://pdfs.semanticscholar.org/c585/52826efc9c3e7f6722b9905057a41e772aeb.pdf)

## 계절 ARIMA 모형을 이용한 화장수요예측: 수원시를 중심으로

기존 장사시설 수요예측 연구에서는 초기발전단계, 중간확산단계, 최종포화단계로 성장형태를 보인다. 

이때 이를 설명하는 방법으로서 콤페르츠 곡선(Gompertz curve)이나 로지스틱 곡선(logistic curve)을 사용하였다. 

이 방법의 핵심은 바로 최종포화단계에서의 크기를 주로 관찰치를 시간에 따라서 각 곡선의 모수를 찾아내는 것이다. 

하지만 현재의 장사시설의 예측은 초기 산업의 발전단계가 아닌 많은 발전을 성과를 이룬 상태–대표적으로 화장률의 경우 1994년에는 20.5% 이었고, 2001년은 38.5%, 2015년은 80.8%–이기 때문에 이제는 보다 정확도가 높은 장래 수요예측으로 특히 화장시설 특성에 맞는 최적 모형 개발이 필요하다. 

이러한 이유로 적은 자료양과 다양한 패턴을 가진 시계열 자료에도 적용 가능한 ARIMA 모형을 제안한다. 최근의 관측값이 미래의 예측력에 영향력이 크다는 개념으로 출발하여 자기회귀 모형(autoregressive model, AR)과 이동평균 모형(moving average, MA)이 결합한 형태를 말한다

## 이론적 배경

### 수요예측

수요예측에 사용하는 모형은 크게 정성적 모형과 정량적 모형, 그리고 두 가지 이상의 모형을 복합 적용하는 결합 모형으로 나눌 수 있다.

그리고 수요예측에 사용되는 모형은 다양하고 동일한 자료로 예측모형이나 사용되는 변수에 따라 다른 결과로 예측될 수 있기 때문에 
모형의 특성을 이해하고 예측 대상과 목적에 맞는 모형을 찾는 것이 중요하다(Table 1).

우선, 정성적 모형은 중장기적 예측에 적합하며, 일반적으로 예측기법의 적용에 소요되는 비용과 시간이 높은 편이고 연구자의 주관이나
판단 또는 여러 사람들의 의견을 종합하여 수요를 예측하는 모형이다.

그렇기 때문에 과거 자료가 부족할 때 사용할 수 있으며, 직관적인 전문가적 식견 반영이 가능하다. 

반면 연구자의 주관적인 개입이 가능하며, 정책적인 의지로 외부의 주관적 환경 반영으로 왜곡된 결과가 나타날 수 있다.

정량적 모형은 수집된 과거자료를 바탕으로 통계적 또는 계량적으로 미래수요를 예측하는 방법이다. 

즉, 과거 자료를 통하여 과거의 변화와 동향을 파악하고 변수 간의 관계를 규명하는 등의 방법으로 예측하는 모형이다. 

이런 모형은 추세분형이 있는데 과거 추세가 계속 지속된다는 가정에 따라 과거의 패턴을 분석하기 위한 형태에 적합한 함수형태를 적용하는 모형이다. 

Box and Jenkins [8]는 ARIMA 모형의 이점6)으로서 간결성(parsimony)을 제시하였는데 이는 가능한 최소의 추정 파라미터 사용하고 적정한 차수의 아카이케정보기준(Akaike Information Criterion, AIC)을 사용한다. 

사실 시계열이 가지는 특징은 불충분한 자료의 수, 외부잡음(white noise)의 상당한 개입, 일정 시간간격이 아닌 자료수집, 비정상성을 가지는 경우가 많아 시계열분석과 예측을 근본적으로 어렵게 만드는 요인이 된다[9].

또한, ARIMA 모형은 단기예측에 주로 사용되는데 이는 ARIMA 모형은 먼 과거보다는 최근 시점에 가까운 과거 관측값에 더 많은 비중을 주기 때문이다. 

최근 시점에 가까운 과거에 비중을 더 준다는 것은 AIRMA 모형에 근거하여 얻어진 장기 예측값이 단기 예측값에 비해 신뢰성이 적기 때문이다[10].


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220312_182622_1.png")
```




    
![output_5_0]({{ site.gdrive_url_prefix }}1ZLcom9hJPzjrNsGQxDlAKFkhg-dsMvGO)




## 자료 분석

본 연구는 수집된 자료를 SPSS 21.0 프로그램(SPSS Inc., Chicago, IL,USA)을 사용하여 분석하였다. 

그리고 사용된 관측데이터를 연구모형의 단계에 따라 분석하였다. 

우선, 정상성(stationarity)을 알아보기 위하여 주어진 관측데이터에 대하여 시계열자료의 정상성을 만족해야한다. 

일반적으로 정상성이란 데이터의 일정한 주기로 나누었을 때, 각 주기에 해당하는 평균과 분산이 일정한 것을 의미한다. 

이때 분산과 평균이 비정상적일 경우 정상성을 만족시키기 위해서 각각 변수 차분과 변화 등으로 이를 만족시켜야 한다[10]. 

또한 분산이 비정상적인 계열로 나타나면 보통 자연로그를 취하게 되면 정상성이 회복된다. 

그렇지 않을 경우 평균의 정상성을 위하여 차분을 수행하게 되는데 1차 차분은 자주 사용한다

다음으로 모형에 대하여 식별은 두 개의 이론적 상관함수인 자기상관함수(auto correlation function, ACF)와 편자기상관함수(partial auto
correlation function, PACF)를 이용하여 추정된 ACF와 PACF가 두 개의 이론적 상관함수와 각각 일치하는지를 비교하게 된다. 

이 단계는 관측값들 사이에 존재하는 상관관계를 측정하여 ARIMA (p,d,q) 모형을 구성하는 자기회귀(AR) 요소인 p와 이동평균(MA) 요소인 q를 선택하는 단계로, ACF와 PACF의 형태에 따라 모형을 식별하게 된다.

그리고 추정단계의 경우 앞서 고려한 모형의 계수를 추정하고, 모수가 통계적으로 유의한지를 판단하게 된다. 

그리고 파라미터를 추정하고, 시계열 자료가 그 모형에 얼마나 잘 적합되는지를 진단한다. 

또한 적합도가 좋은 모형 측정하는 통계량인 R2와 정상 R2가 있는데, 시계열 자료가 특정적인 추세가 있거나 분산을 정상적으로 만들기 위해
변수변환을 실시했다면 정상화R2 사용을 한다.

마지막으로 모형진단에서는 모형의 적합성을 점검하게 된다. 

이때통계적으로 적절한 모형인지는 백색잡음(white noise)7)들이 서로 독립(즉, 자기상관되어 있지 않음)이어야 하며, 이를 검증하기 위해 Box와 Ljung의 통계량값 및 이상값 수를 확인하게 된다.


```python

```

### 연구 결과


#### 정상성 및 계절성 점검

순차도표 분석 결과, 관내 화장장 이용의 수요는 2001년부터 2016년까지 월 평균 0.9%, 연 평균 11.3%로 증가 추세였다. 

그리고 이러한 관내 수요는 보통 3년마다 있는 윤달에 맞춰 평소 2-3배 증가하기도 하였다. 

이렇듯 관내 화장장 이용 실적은 전반적으로 평균이 증가하는 추세이긴 하나 분산 또한 전체 수준이 커지기 때문에 평균과 분산이 모두 비정상적이라 할 수 있다. 

이렇듯 시계열 자료와 같이 분산이 비정상적이면서 평균 또한 비정상적일 경우 변수변환을 사용하여 분산을 정상적으로 만든 후 그 다음 차분을 통해 평균을 정상적으로 만들어야 한다

### 모형 진단 및 예측결과

모형진단은 추정된 모형에 대하여 통계적으로 적합한지를 판단하는 단계인데, 이때 자기상관함수와 검증 통계량을 통하여 이를 검증하게 된다.

우선, 수원시 연화장의 관내이용자 수요에 대한 ARIMA 모형의 통계량은 Table 5와 같으며 ARIMA(0,1,1)(1,1,0)S =12 모형의 LjungBoxQ(18)란의 유의확률값인 0.544로 유의수준 5%보다 월등히 크기때문에 백색잡음항이 독립이며, 귀무가설을 채택할 수 있게 된다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220312_190527_1.png")
```




    
![output_11_0]({{ site.gdrive_url_prefix }}1tYqlapM6nXgUUXhDYrhEF0J_2B1trULu)



### 본 논문에 관련된 추가 관련 자료 

- 출처 : [https://predictor-ver1.tistory.com/3](https://predictor-ver1.tistory.com/3)

> 분산이 비정상적이던가 평균이 비정상적이던가 하면 정상성을 해줘야 함

대부분의 시계열 자료는 다루기 어려운 비정상성 시계열 자료이기 때문에
분석하기 쉬운 정상성 시계열 자료로 변환

(1) 평균이 일정 : 모든 시점에 대해 일정한 평균을 가진다.

- 평균이 일정하지 않은 시계열은 차분(difference)을 통해 정상화

- 차분은 현시점 자료에서 이전 시점 자료를 빼는 것

(2) 분산도 시점에 의존하지 않음

- 분산이 일정하지 않은 시계열은 변환(transformation)을 통해 정상화

(3) 공분산도 시차에만 의존할 뿐, 특정 시점에는 의존하지 않음

> 차분(I)

- 정상 시계열화 하기 위해 1차 차분을 먼저 한 후 정상성이 되지 않으면 2차차분으로 식으로 넘어가야한다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220312_184245_1.png")
```




![output_15_0]({{ site.gdrive_url_prefix }}1WGGPMPQp743m6gThW__TFlAH1I-Zum8O)




> 정상성을 확인하는 방법 

- ADF 검정법으로 테스트 하는 방법이 있다.


- 파이썬으로 ADF검정하는 방법 


```python
# EXAMPLE
from statsmodels.tsa.stattools import adfuller
y = gold_price_series['price(USD)']
y_1diff = gold_price_series.diff().dropna()['price(USD)']
result = adfuller(y)
print(f'원 데이터 ADF Statistic: {result[0]:.3f}')
print(f'원 데이터 p-value: {result[1]:.3f}')
result = adfuller(y_1diff)
print(f'1차 차분 ADF Statistic: {result[0]:.3f}')
print(f'1차 차분 p-value: {result[1]:.3f}')
```

차분을 하지 않은 원 데이터는 p-value가 유의 수준인 0.05보다 크므로 정상 시계열로 볼 수 없다.


이에 비해 1차 차분의 데이터는 p-value가 유의 수준보다 작으므로 정상 시계열로 볼 수 있다.

이로서 차분(d)의 차수는 1인 ARIMA(p, 1, q)의 모델 구축이 필요하다는 것을 알 수 있다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220312_184916_1.png")
```




    
![output_20_0]({{ site.gdrive_url_prefix }}1nX9QHyZNg8BEJAp-hzh-w_jWLzT5VVl4)




- 그림 출처:[https://predictor-ver1.tistory.com/3](https://predictor-ver1.tistory.com/3)

> AR(p), MA(q)를 정하는 방법

- 일반적으로는 ACF (AutoCorrelation Function)와 PACF (Partial AutoCorrelation Function) 그래프를 이용해서 정한다.

- 파이썬으로 AIC를 기준으로 최적의 차수를 정하는 방법


```python
# example
def my_auto_arima(data, order,sort = 'AIC'):
 order_list = []
 aic_list = []
 bic_lsit = []
 for p in range(order[0]):
 for d in range(order[1]):
 for q in range(order[2]):
 model = ARIMA(data, order=(p,d,q))
 try:
 model_fit = model.fit()
 c_order = f'p{p} d{d} q{q}'
 aic = model_fit.aic
 bic = model_fit.bic
 order_list.append(c_order)
 aic_list.append(aic)
 bic_list.append(bic)
 except:
 pass
 result_df = pd.DataFrame(list(zip(order_list, aic_list)),columns=['order','AIC'])
 result_df.sort_values(sort, inplace=True)
 return result_df


my_auto_arima(gold_price_series,[3,3,3])
```


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220312_185526_1.png")
```




![output_25_0]({{ site.gdrive_url_prefix }}1Fb-TGlslH3NoEvYudbvbUbipoxLOiNwL)

    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220312_185526_2.png")
```




    
![output_26_0]({{ site.gdrive_url_prefix }}15G5Su4G-3MZSlmg-qI6KqjtSzOWuPuNv)
   



ARIMA(2,1,2)가 최적의 모델임을 확인할 수 있다. 실제로 모델의 fitting결과를 확인해 보자. 일단, 금 시세는

y't = 0.2620(1-0.7617+0.9486) + 0.7617 y't-1 - 0.9486 y't-2 + et - 0.7777et-1 +0.9608et-2를 따르는 ARIMA모델임을 확인할 수 있다.

모델의 Fitting 결과를 시계열 그래프로 확인해 보자.


```python
model = ARIMA(gold_price_series, (2,1,2))
model_fit = model.fit()
print(model_fit.summary())
model_fit.plot_predict()
plt.show()
```


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220312_185824_1.png")
```




    
![output_29_0]({{ site.gdrive_url_prefix }}1ZAwNQJOAyRjMdgjtA4DQkUCt68ytC4fU)





```python
model_fit.plot_predict(start=datetime.datetime(2020,1,3))
plt.show()
```


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220312_185824_2.png")
```







![output_31_0]({{ site.gdrive_url_prefix }}1M7LLJ0W2pl45K9wMccqdvg3Dg3FPRLHf)




- 2019년까지의 데이터로 모델을 fitting 하고 2020년 3월까지의 값을 예측해 보도록 하자.


```python
train, test = gold_price_series.loc[:datetime.datetime(2019,12,31),:], gold_price_series.loc[datetime.datetime(2019,12,31):,:]
model = ARIMA(train,(2,1,2))
model_fit = model.fit()
full_forecast = model_fit.forecast(steps=test.shape[0])
forecast = pd.DataFrame(full_forecast[0], index=test.index, columns=test.columns)
plt.plot(train.loc[datetime.datetime(2018,12,31):,:])
plt.plot(test)
plt.plot(forecast)
```


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220312_190020_1.png")
```




  
![output_34_0]({{ site.gdrive_url_prefix }}1uoSbmkLLA2tLgAMVZFbRoKPqXadcYGhi)




