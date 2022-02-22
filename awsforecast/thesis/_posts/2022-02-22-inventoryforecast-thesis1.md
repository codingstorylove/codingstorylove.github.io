---
layout: single
title: "재고예측 및 수요예측 논문 정리 및 공부 1"
date: "2022-02-22 16:36:10 +0900"
last_modified_at: "2022-02-22 16:36:10 +0900"
---

# 재고예측 및 수요예측 논문 정리 및 공부 1

## 논문 1


- 출처 : [상하향식 통합 수요예측 기반 반도체 설비부품 재고관리 시스템 개발 = Top-bottom Integrated Demand Forecasting Based Development of Semiconductor Equipment Parts Inventory Management System](http://www.riss.kr/search/detail/DetailView.do?p_mat_type=be54d9b8bc7cdb09&control_no=612aaab51d597a36ffe0bdc3ef48d419&outLink=K)


- 저자 : 김우재

거시적인 설비군 판매량을 예측하기 위해서는 시계열 분석 방법 중에 하나인
ARIMA 모델을 사용하였고, 개별적인 부품의 수요량을 예측은 KNN Regression을
통해 진행을 하였다. 특히 KNN Regression을 사용하기 전에는 개별적인 부품들을
K-means 군집화를 통해 군집화한 후 군집화된 부품들에 대해 KNN Regression을
적용하는 방법을 사용하였다.

## 자기회귀누적이동평균 (ARIMA)

데이터가 비정상성(non-stationarity)을 가지는 경우에도 차분을 통해 정상성을 가
지는 데이터로 변경을 하여 분석을 할 수 있다는 장점 때문에 ARIMA 모델은 다양
한 분야에서 미래를 예측하는 모델로 사용되었으며 ARIMA 모델과 관련된 모델들은
그림 3과 같다. Zhu et al. (2018)은 ARIMA 모델을 통해 중국 GDP의 성장을 예측하
여 2008년 쓰촨성 대지진이 발생한 후의 거시적인 경제 회복을 평가하였다.
2008~2014년 지역 GDP 시뮬레이션 수치와 재난 이후 실제 지역 GDP 데이터를 비교
한 결과, 가장 심각한 지역 중 한 지역만이 재난 전 수준으로 회복 되었다는 것을 알
수 있었다. 이성우·임재형 (2019)은 한국과 미국의 협력관계의 변화에 대하여 예측하
였다. 데이터는 한국과 미국의 관계에 대해 보도한 언론들의 자료를 수집하고 분석한
GDELT(Global Data on Events, Location and Tone, 1929-2012)를 사용하였다. 그리
고 분석한 자료를 통해 분쟁의 정도를 종속변수로 두어 한국과 미국의 우호관계를
예측할 때 ARIMA 모델을 사용하여 분석하였다.

수요예측을 통해 재고관리를 원활하게 하고자 하는 연구들 또
한 진행이 되었다. 박영진·전건욱 (2008)은 항공기 수리부속품에 대한 수요를 예측하
는 연구를 진행하였다. 그 결과 기존에 공군에서 사용되던 가중이동평균법, 선형이동
평균법, 추세 분석법, 단순지수평활법, 선형지수평활법 보다 ARIMA 모델이 더 우수
한 예측력을 보였다. Udom & Phumchusri (2014)은 플라스틱 공장의 판매량을 정확
하게 예측을 하여 재고비용을 줄이기 위해 판매예측 방법으로 ARIMA 모델과 이동
평균기법, 지수평활법을 비교하는 연구를 진행하였다. 5개의 제품판매량 데이터에 대
해서 적용하여 평균 절대 백분율 편차(MAPE, Mean Absolute Percentage Error)로
비교한 결과 ARIMA의 MAPE가 37.64%로 가장 낮은 수치를 보였다. 김정아 외
(2018)은 비료 공급 및 생산 업체의 재고 부족 및 과잉 생산을 줄이기 위해 ARIMA
모델을 사용하였다. 그리고 ARIMA 모델을 사용하여 얻은 수요예측 값을 토대로 서
비스 수준을 95%, 99%를 달성했을 때를 시뮬레이션 하였다. 95% 서비스 수준을 달
성했을 때는 기존의 경제적 주문량 모델보다 재고부족횟수가 10회에서 2회로 감소하
였고, 99%인 경우 또한 8회에서 2회로 줄어드는 것으로 나타났다

## 연구모형

수요예측에 관련된 연구들은 많이 진행이 되었지만 다른 분야들에 비해 반도체 설
비 부품의 수요 및 판매량에 대해 진행된 연구는 부족하였다. 또한 머신러닝의 발전
으로 인하여 수요예측의 정확성이 높아질 수 있지만 현재 회사나 기업에서 사용할
수 있는 데이터 양은 한계가 있어 머신러닝 기법만을 적용하기는 어렵다.
따라서 전통적으로 사용되는 수요예측 방법과 머신러닝 수요예측 방법을 모두 적절하게 사용해야 한다(Fu et al., 2018). 이러한 점들을 고려하여 본 연구에서는 ARIMA 모델과 KNN Regression을 동시에 사용하여 반도체 설비 부품의 수요 및 판매량을 예측하고
자 하였다.


그 이유는 다음과 같다. 먼저 ARIMA 모델과 KNN Regression이 가지고 있는 강
점의 분야가 다르기 때문이다. ARIMA 모델의 경우 외생적인 변수들의 영향을 줄여
서 분석을 할 수 있기 때문에 거시적인 측면에서 분석을 할 때 유리하다(Zhu et al.,
2018). 반면에 KNN Regression의 경우 End-consumer들에 대해서 높은 예측률을 보
인다(Valgaev, et al., 2017). 즉 거시적인 측면보다는 미시적인 측면을 분석을 할 때
강점을 가진다. 따라서 본 연구에서는 두 가지 모델의 각기 다른 장점을 모두 활용하
기 위해 두 개의 모델을 통해 상하향식 통합 수요 예측 모델을 구축하고자 하였다.
제품군의 판매량을 예측하는 방법은 두 가지가 있다. 먼저 제품군 전체의 판매량의
데이터를 사용하여 다음 달 제품군 전체의 판매량을 예측하는 방식이다. 두 번째는
제품군을 이루고 있는 개별 제품들의 판매량을 사용하여 개별 제품들의 다음 달 판
매량을 각각 예측한 후에, 이를 모두 합치는 방법이다. 첫 번째 방법의 경우는 거시
적인 측면에서 예측을 진행하는 방식이고, 두 번째 방법의 경우는 미시적인 측면에서
예측을 진행하는 방법이다. 따라서 본 연구에서는 첫 번째 방법의 예측수단으로는
ARIMA 모델을 사용하였고, 두 번째 방법의 예측수단으로는 KNN Regression을 사
용하였다. 그리고 도출된 두 개의 예측 값을 비교한 후에 KNN Regression을 통해
예측한 개별 부품의 수요 및 판매량을 ARIMA 모델로 예측한 값과의 오차만큼 보정
하였다. 이를 통해 KNN Regression 수요예측의 불확실성을 줄이는 효과를 얻을 수
있기 때문에 보다 정교한 수요예측을 진행할 수 있다


## K-means clustering

또한 본 연구에서는 KNN regression을 진행하기 전에 K-means clustering을 통해
부품별로 군집화를 하였다. 반도체 설비 부품의 특성상 같은 제품군, 또는 같은 특성
을 가지는 부품들끼리의 판매량 상관관계가 매우 높다. 그러나 KNN regression을 바
로 사용하게 되면 이러한 특성이 반영이 되지 않고, 판매량의 증감만을 고려하게 된
다. 따라서 서로 전혀 상관이 없는 부품이더라도 그 부품의 판매량의 예측치가 영향
을 미치게 되는 것이다. 이전 연구들에서도 수요예측을 진행하기 전 데이터의 noise
를 줄이고 정확도를 높이고자 예측하기 전 군집화를 진행하였으며(Dong et al.,
2017), 군집화를 하고 예측을 하는 경우에 정확도가 더 높은 것으로 나타나기도 하였
다(노윤지, 2020; Hadavandi et al., 2011). 따라서 본 연구에서는 K-means clustering 을 통해 군집화를 한 후 예측을 진행하여 보다 정확한 수요예측을 진행하고자 하였다.



## 결과 비교

사용된 수요예측 모델은 이동평균, ARIMA, KNN Regression 순으로 사용이 되었
으며, KNN Regressoin의 경우 맨해튼 거리와 유클리디언 거리를 사용하였다. 그 결
과는 표 19, 표 20, 표 21 그리고 표 22과 같다. 전반적으로 4가지 모델에서 모두 1월달의 MAPE가 높게나와 예측의 정확도가 낮은 것으로 나타났다. 이는 A사의 특징
때문인 것으로 나타났는데, A사와 거래하는 회사들의 경우 회계문제로 인해 12월에
필요한 주문량을 1월에 주문을 하는 경우가 잦았다. 이로 인하여 1월의 예측의 정확
도가 상대적으로 낮았으며, 1월을 제외한 다른 달에는 정확도의 큰 차이가 없었다. 4
가지 모델에서 개별 부품의 수요예측 정확도를 나타내는 MAPE는 유클리디언 거리
를 사용한 KNN Regression이 가장 정확하게 나타났다. 하지만 전량보유확률에서는
이동평균을 사용한 경우에 가장 정확도가 높게 나타났다. 반면 평균적인 재고비용은
KNN Regression을 사용한 경우가 대체로 더 높았으며, 맨해튼 거리를 사용한 KNN
Regression이 가장 높게 나타났다. 그러나 이는 실제 수요량보다 이동평균과 ARIMA
모델로 예측하였을 때 KNN Regression보다 재고량를 낮게 예측하는 경향으로 인해
재고비용도 낮게 나타난 것으로 분석되었다. 기본 수요예측 모델의 경우 전량보유확
률 보다는 개별 부품에 대한 정확도가 높은 경우가 더 중요하다고 판단되었다. 따라
서 본 연구에서는 군집화 후 각 군집에 대하여 수요예측을 진행할 때, 표 22과 같이
유클리디언 거리를 사용한 KNN Regression을 기본 수요예측 모델로 설정하였다.





```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_145111_1.png")
```




![output_3_0]({{ site.gdrive_url_prefix }}1AHiIjUC8L-uF7-4YXU4w0fvUh236c1AK)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_145111_2.png")
```



![output_4_0]({{ site.gdrive_url_prefix }}1KbA7iXOvJ5oY_shimEnICuJbwumGMZSw)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_145111_3.png")
```



![output_5_0]({{ site.gdrive_url_prefix }}135mTysB9lAzBNGeauwxLE5qC86Mn2KCG)
    



실험 결과 A반도체 설비 부품 회사의 Etcher 부품군은 K-means 군집화를 통해
총 7개의 군집으로 나눌 수 있었다. 군집화의 적합성 여부는 강건성을 통해 확인하였
으며, 2019년 10월부터 2020년 2월까지의 데이터로 검증하였을 때, 7개의 군집으로
나누는 경우에 강건성을 갖는 것을 확인할 수 있었다. 그 후 상향식 예측 모델을 정
하기 위해 여러 모형들을 비교분석하였다. 그 결과, 전통적인 수요예측 방식인 이동
평균기법과 ARIMA 모델보다 유클리디언 거리 기반의 KNN Regression을 사용한
경우에 수요예측의 정확도가 더 높은 것으로 나타났다. 또한 전체 부품에 대해 유클
리디언 거리 기반의 KNN Regression으로 수요예측을 한 경우보다 군집화 후 유클리
디언 거리 기반의 KNN Regression으로 수요예측을 진행한 경우 수요예측의 정확도
가 더 높아진다는 것을 확인할 수 있었다. 마지막으로 상하향식 통합 수요예측 모델
과 군집화 후 KNN Regression으로 수요예측을 한 경우를 비교하였다. 상하향식 통
합 수요예측 모델로 수요예측한 결과, 부품군의 수요예측 정확도가 향상되었으며, 정
확도의 안정성도 올라가는 것을 확인할 수 있었다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_150000_1.png")
```



![output_7_0]({{ site.gdrive_url_prefix }}18wD-3yd4PlEsTmpQw317F9XVYiwmTZXp)



## 논문 2

- 출처 : [딥러닝 기법을 활용한 가구 부자재 주문 수요예측 원문보기 KCI 원문보기 인용
Demand Prediction of Furniture Component Order Using Deep Learning Techniques](https://scienceon.kisti.re.kr/srch/selectPORSrchArticle.do?cn=JAKO202022349916778)

기업 측면에서 수요의 예측은 생산, 자재 및 
물류 등의 계획과 관리 측면에서 중요하다. 
대부분의 중소 제조 기업은 다품종 소량생산 형태
로 고객의 주문을 대략 예측하여 자재를 대량 
구매하고 주문에 따라 재가공하여 판매하는 형태이다. 

자재 주문의 리드 타임이 비교적 길어지게 되면 고객의 니즈를 대응하기 어렵다. 고
객의 니즈와 트렌드 변화에 따라 정확한 리드 
타임 파악과 적절한 주문량 파악을 할 수 있는 
재고관리가 이루어져야 한다. 이를 통해 현장에
서 발생하는 다양한 변수를 예측하여 효율적인 
재고관리가 가능하다[4].
수요 예측은 불규칙한 시계열 특성이 있으며, 
정확한 수요 예측은 재고관리와 직결되는 문제
로 수요 예측을 잘하면 불필요한 재고를 보유할 
필요가 없어 과잉생산으로 인한 물류, 재고 비
용을 줄여줄 수 있다. 하지만 정확한 수요를 예
측하기 위해서는 불규칙적으로 변화하는 경제동향, 시장 동향, 사회적 이슈 등 외부요인을 모두 고려해야 하기 때문에 어려운 문제이다. 김
정아, 정종필, 이태현, 배상민(2018)은 ARIMA 
모형을 이용하여 계절적 요인과 같은 시간적인 
변동성을 찾아 수요를 예측하고 이를 통해 경제
적 주문량 모형 기반의 수요 예측 모델을 제안했다[4].

최근 인공지능 기술의 발전으로 불규칙한 시계열 데이터에 대하여 딥러닝 알고리즘을 이용하여 시계열 데이터의 연속성을 스스로 찾아내도록 하는 방법[5, 6]이 활발하게 연구되고 있다. 

김진섭, 황재성, 정재우(2020)는 시계열 분해 데
이터를 이용한 LSTM 기법 기반 항공기 수리부
속 수요 예측 방안 연구에서 시계열 데이터가 
선형적인 특성을 보이는 Smooth 한 수요패턴에 
대해서는 전통적 통계 기법도 수요 예측 정확도가 비교적 우수하지만, 수요가 불규칙한 Erratic, Lumpy 수요패턴에 대해서는 통계적 방법으로 
수요를 예측하는데 제한이 있고, LSTM 기법이 
우수하다는 연구결과를 보여주고 있다[7]. 또한, 
많은 연구에서 전력수요 예측을 비롯하여 불규칙한 시계열 데이터 연구에서는 인공지능 딥러닝 알고리즘이 기존 ARIMA 모형보다 더 좋은 
성능을 내고 있다[8, 9, 10].

시계열 데이터를 활용하여 수요를 예측하는 
방법에는 크게 통계적 분석방법과 인공신경망 
기반의 머신러닝, 딥러닝 기법으로 나눌 수 있다.

통계적 분석방법으로는 시계열 자료의 추세요인과 계절 변동요인 등을 예측에 반영하는 지
수평활법(Exponential Smoothing), 계절성 요인을 고려하는 Holt Winters, 지수평활법을 수정한 
Croston기법, 시계열 자료의 관심변수 또는 차분변수가 자귀 회귀 과정, 이동평균 과정과 두 과정
의 결합된 과정을 따르는지를 식별하는 ARIMA(Auto Regressive Integrated Moving Average) 모형 등이 있다[7].

ARIMA(Auto-regressive integrated moving average, 자기 회귀 누적 이동평균) 모형은 AR(Auto-regressive process, 자기 회귀)과정과 MA(Moving Average process, 이동평균) 과정을 혼합한 모형으로, 차분(differencing) 절차를 통해 정상화한 후 분석 및 예측을 수행한다. 

과거의 데이터와 그 데이터가 지니는 추세를 반영하여 시계열 예측을 수행한다는 점에서 기존의 시계열 분석 방법과 차이가 있다.

ARIMA 모형에 주기적 특성이나 계절성을 반영한 것이 SARIMA(Seasonal Auto-regressive integrated moving average) 모형이다. 

예측하고자 하는 시점에서 가까운 과거의 자료들만을 이용하는 ARIMA 모형과 달리, 이전 주기의 자료를 추가로 활용한다[9].

### 1D-CNN 예측 모델

CNN의 필터(feature detector)가 원본 데이터 위에서 움직이는 과정을 convolution
이라 하며, 필터가 움직이는 방향에 한 방향인 
CNN을 1D-CNN이라 한다. 

1D-CNN은 전체 데이터 중 길이가 고정된 짧은 구간에 대해 패턴을 인식하는 경우에 효과적이다. <그림 2>는 
1D-CNN의 구조도이다. Convolution layer에서 
입력된 시계열 데이터와 인접한 데이터를 필터링한 후 convolution layer의 output feature map을 형성한다. 

Pooling layer에서 feature의 차원축소를 위해 특징을 잘 설명하는 고정 벡터를 선별
하여 시계열 데이터에서 중요한 특징을 추출한다




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_155006_1.png")
```



![output_15_0]({{ site.gdrive_url_prefix }}11FAWMY5ZgrGglpZRy3MDEdmwQM9SiVwh)
    



예측 모델인 ARIMA 모델과 인공지능기반의 
예측 모델인 단변량 LSTM, 다변량 LSTM, 
1D-CNN을 구축한 뒤 각각의 예측모형에 대해 
세 가지 성능평가 지표로 정확성을 평가한다.

본 연구에서는 A사의 2016년도∼2020년 6월
까지의 과거 4년간 제품군별 판매 수량 데이터
를 사용하였다. 제품의 판매 수량은 월 단위로 
집계했을 때 각 제품의 판매 수량이 연간의 추세를 가지는 것이 확인되어 예측 모델에서는 제품군 B에 대한 월별 판매 수량의 합계를 사용하
였다. 데이터의 예시는 <표 1>과 같다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_155308_1.png")
```



![output_17_0]({{ site.gdrive_url_prefix }}1HP1QAT_6pATwwW0L1ty0zK2VctjLJKjd)
    



### 성능평가 지표

예측 모델의 정확도를 평가하기 위해 본 논문은 성능평가 지표로서 MAPE(Mean Absolute 
Percentate Error)와 RMSE(Root Mean Squared 
Error), RMSLE(Root Mean Square Logarithmic 
Error)를 사용하였으며 각 예측 모델에 대해 계산하였다.

- MAPE

MAPE(Mean Absolute Percentage Error)sms 
실제값에 대한 오차의 비율을 모두 합한 다음 
기간 수로 나눈 값이므로 기간에 따른 값들의
크기가 크기 달라질 때 유용한 예측 오차 측정 
방법이다.



- RMSE

RMSE(Root Mean Squared Error)는 실제값과 
예측값 차이의 제곱 합을 기간 수로 나눈 뒤 제곱근을 한 값으로 양의 오차와 음의 오차가 상쇄되지 않는다는 장점이 있는 성능평가 지표이다

- RMSLE

RMSLE(Root Mean Squared Logarithm Error)는 실제값과 예측값의 차이를 제곱해 평균한 것에 루트를 씌운 RMSE에 로그를 적용한 지표이다. 

RMSLE는 아웃라이어에 강하고 예측값과 실제값의 상대적 오차를 측정해준다

## 실험 및 결과

본 논문은 통계적 기법의 시계열 예측 모델과 
인공지능 기반의 시계열 예측 모델을 구성하여 
정확성 평가를 수행하였다. 

A사의 제품군 B에 대한 월별 판매 수량 데이터에서 2016년 1월부터 2019년 6월까지를 훈련 데이터로 설정하고, 
최근 1년의 기간인 2019년 7월부터 2020년 6월까지의 데이터를 검증 데이터로 나누어 실험을 진행하였다.


### ARIMA 기법을 이용한 분석모델


ARIMA 모델은 (p, d, q)와 같이 표현되는데 
여기서 p는 AR(Auto-regressive) 항의 차수를 의미하고, q는 MA(Moving Average) 항의 차수를 
의미하며, d는 차분의 차수를 의미한다. 본 연구에서는 ARIMA의 Best model을 pmdarima. auto_arima로 추정하였다. Best model을 추정한 결과 ARIMA(1,0,0)가 산출되었다. 

그리고 이를 이용하여 매출 데이터를 종속변수로 설정하여 예
측 분석을 수행하였다.



```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_160354_1.png")


```



![output_21_0]({{ site.gdrive_url_prefix }}1id3So3ptCPReHd9T8WsgEUJ--kLOxkr9)




### LSTM 기법을 이용한 예측 모델

LSTM을 이용한 예측 모델은 단변량 예측 모델과 다변량 예측 모델로 구성하였다. 

두 LSTM 모델 모두 Month를 더미 변수화하여 sequence 
데이터로 만들어 딥러닝에서 용이하게 사용할 수 있도록 하였다. 단변량 LSTM 예측 모델의 
활성화 함수는 ReLU를 사용하였으며, 마지막 
Hidden Layer에서 50%의 Dropout Layer를 삽입하여 무작위성을 추가하였다. 

<그림 4>와 같이 
단변량 LSTM 예측모형은 월별 판매 수량과 
Month의 더미 변수만을 입력변수로 활용하였다. 



```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_160800_1.png")


```



![output_23_0]({{ site.gdrive_url_prefix }}1MrwJNo3dsdzSbDfdXF08IhprrZdNKlyu)

    



각 Hidden Layer에서 1024개의 노드를 만들어내도록 설정하였으며, 활성화 함수는 ReLU를 
사용하였다. Hidden Layer에 Dropout Layer를 삽입하여 학습에 무작위성을 주었다. 

Output Layer에서는 sigmoid 함수를 사용하여 결과 값이 0에
서 1 사이의 값을 갖도록 지정하였다.


```python

from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_161414_1.png")


```





![output_25_0]({{ site.gdrive_url_prefix }}1Hx2uBFB_t6Ww5ucqtqtfpkKP53H8mpF1)
    



단변량 LSTM의 경우 MAPE가 5.41%로 약간 
낮은 성능을 보이지만 ‘Remodeling’의 언급 문서 수와 건설공사비 지수, 전월 판매 수량을 설명변수로 추가한 다변량 LSTM의 경우 MAPE 
2.81%로 감소하여 설명변수를 추가했을 때 더
좋은 예측결과를 보이는 것을 확인할 수 있었다. 

RMSE와 RMSLE 또한 변수를 추가한 예측에서 
각각 821,529과 0.018씩 낮아짐을 확인하였다.

### 1D-CNN 기법을 이용한 예측 모델

1D-CNN 예측 모델은 월별 판매 수량만을 입력변수로 구성하였으며 LSTM 예측 모델과는 달리 Month 변수를 더미화하지 않았다. <그림
7>과 같이 1D-CNN 예측 모델은 과거 3개월의 
판매 수량을 입력 변수로 재구조화 하였다. 출력공간의 차원은 64이고, 1D convolution의 창은 2로 설정하였다. 활성화 함수는 ReLU를 사용하였다. 


```python


from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_161956_1.png")


```



![output_28_0]({{ site.gdrive_url_prefix }}1FLDbBCTokHGPLzRAD085fowRZH6Q1Djm)




```python


from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220222_162401_1.png")


```



![output_29_0]({{ site.gdrive_url_prefix }}1B7Fl0aQHreOQFN4pnFdnSd0NFpEEWMZQ)



분석 결과에서 월별 판매 수량만을 입력변수로서 고려한 시계열 예측 모델 중 
1D-CNN의 예측 성능이 가장 우수한 것으로 나타났다. 

하지만 전월의 뉴스와 블로그에서 리모델링이 언급된 문서의 개수를 집계하고, KOSIS에서 전월의 월별 건설공사비 지수 등 다양한 
항목을 추가하여 판매 수요 추세를 반영할 수 있는 다변량 LSTM 예측 모델의 성능이 월별 판매 수량만을 고려한 1D-CNN 예측 모델의 성능보다 우수한 것으로 드러났다. 

따라서 수요 예측 시 추세를 반영할 수 있는 다양한 변수들을 
통해 다변량 LSTM을 실시하는 것이 바람직하며, 제한적인 조건으로 인해 하나의 입력변수를 고려할 수밖에 없는 경우 1D-CNN을 실시하는 
것이 바람직할 것이다.
