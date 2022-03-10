---
layout: single
title: "ARIMA 알고리즘 관련 논문 공부 및 정리 02"
date: "2022-03-10 12:18:10 +0900"
last_modified_at: "2022-03-10 12:18:10 +0900"
---


# ARIMA 알고리즘 관련 논문 공부 및 정리 02



## ARIMA-Intervention 시계열 모형을 이용한 인천국제공항 식음료 매출 분석 및 추정 연구

- 출처 : [http://www.koreascience.or.kr/article/JAKO201913457809410.pdf](http://www.koreascience.or.kr/article/JAKO201913457809410.pdf)

Analysis and Estimation of Food and Beverage Sales at Incheon Int’l

Airport by ARIMA-Intervention Time Series Model

Han-Young Yoon1*, Sung-Sik Park2

1
Division of Comprehensive Aviation Studies, Hanseo University

2
Division of Flight Operation, Korea National University of Transportation

### ARIMA 분석 : 음료(beverage)

계절성과 같은 비정상성을 발견하여 제거하고 안정적이면서 고정적인 추세를 찾아내는 것이 필요하다. 

계절적 및 비계절적 데이터 차분을 1회씩 수행한 후 ARIMA p, d, q 차 수를 결정하기 위해서 자기상관함수 및 편자기상관함수를 분석하였다.

따라서 식사 매출액 예측모형과 마찬가지로MA(0) 및 AR(0) 이라고 차수를 결정하여 최종모형으로 비계절차분과 계절차분이 각각 1로 차수가 결정된 ARIMA(0,1,0) (0,1,0) 모형이 설정되었다. 

식사 매출액 ARIMA 개입모형의 적합도 분석 결과는 Table 3과 같다. 

예측 모형에서 자기상관함수의 잔차의 Ljung- Box 통계량의 p-value 값이 .082로 분석되어 95% 신뢰수준에서 유의하지 않다. 

‘잔차 사이에 자기상관이 없고 잔차의 분포는 독립적(무차별)이다’라는 귀무가설을 채택한다는 의미로 해석된다[5, 8].


```python

from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220308_173501_1.png")
```




    

![output_5_0]({{ site.gdrive_url_prefix }}1mE-q78U-AqvIiYRnu1L__dmP3eIo3z9m)



Fig 13과 같이 자기상관함수 및 편자기상관 함수의 잔차들이 95% 신뢰구간 내에 모두 분포하고 있기 때문에 백색잡음 조건도
충족하는 것으로 파악되었다


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220308_173950_1.png")
```




    
![output_7_0]({{ site.gdrive_url_prefix }}19G3rIOPAeO8l1Px1-YbcZoy9e082B-wT)




### 결론

#### 예측 결과

ARIMA 개입모형을 이용한 식음료 매출액 추정액은 Table 4에 제시되어 있다. 


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220308_181049_1.png")
```




    
![output_9_0]({{ site.gdrive_url_prefix }}1wvvdmcO4IrnBf5IgaodksfG6qFDunu72)



예측은 정상 예측치를 최대값까지 함께 제시하였다.
정상 예측치의 경우 개입변수로 중국의 한한령이 반영되어 상승폭이 다소 둔화된 모습으로 매출액이 추정되었다. 

최대 예측치의 경우 개입변수(외부적 충격)이 반영되지 않았을 경우의 매출액 추정치를 보여주고 있다. 

첫째,식사 매출액의 경우 비록 상승세가 둔화되었다 하더라도
하계 극성수기인 2019년 7월 203억, 2019년 8월 212억으로 월별 매출액이 200억을 돌파할 것으로 예측되며
2020년에는 각각 214억 및 221억으로 증가할 것으로 예측되었다.

둘째, 음료 매출액의 경우 식사 매출액에 비해 상대적으로 우수한 예측력을 보이는 것으로 분석되었는데,
2019년 7월에는 77억, 2019년 8월에는 81억으로 나타났다. 
아울러 2020년에는 79억 및 82억으로 증가할 것으로 판단된다


```python

```

> 논문 쓸때 참고하기 좋은 사이트

1. 월마트의 매출을 예측해 봅시다!

- [https://medium.com/@deepvalidation/%EC%9B%94%EB%A7%88%ED%8A%B8%EC%9D%98-%EB%A7%A4%EC%B6%9C%EC%9D%84-%EC%98%88%EC%B8%A1%ED%95%B4-%EB%B4%85%EC%8B%9C%EB%8B%A4-73faf8dc7db](https://medium.com/@deepvalidation/%EC%9B%94%EB%A7%88%ED%8A%B8%EC%9D%98-%EB%A7%A4%EC%B6%9C%EC%9D%84-%EC%98%88%EC%B8%A1%ED%95%B4-%EB%B4%85%EC%8B%9C%EB%8B%A4-73faf8dc7db)

2. 네이버 수요예측의 비밀 HyperCLOVA

- [https://deview.kr/data/deview/session/attach/5_%E1%84%82%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%87%E1%85%A5%20%E1%84%89%E1%85%AE%E1%84%8B%E1%85%AD%E1%84%8B%E1%85%A8%E1%84%8E%E1%85%B3%E1%86%A8%E1%84%8B%E1%85%B4%20%E1%84%87%E1%85%B5%E1%84%86%E1%85%B5%E1%86%AF%20HyperCLOVA,%20%E1%84%87%E1%85%B5%E1%86%A8%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%E1%84%8B%E1%85%A6%E1%84%89%E1%85%A5%20%E1%84%8B%E1%85%A8%E1%84%8E%E1%85%B3%E1%86%A8%20%E1%84%89%E1%85%B5%E1%84%80%E1%85%B3%E1%84%82%E1%85%A5%E1%86%AF%E1%84%8B%E1%85%B3%E1%86%AF%20%E1%84%8E%E1%85%A1%E1%86%BD%E1%84%8B%E1%85%B3%E1%86%AF%20%E1%84%89%E1%85%AE%20%E1%84%8B%E1%85%B5%E1%86%BB%E1%84%82%E1%85%B3%E1%86%AB%20%E1%84%8B%E1%85%B5%E1%84%8B%E1%85%B2.pdf](https://deview.kr/data/deview/session/attach/5_%E1%84%82%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%87%E1%85%A5%20%E1%84%89%E1%85%AE%E1%84%8B%E1%85%AD%E1%84%8B%E1%85%A8%E1%84%8E%E1%85%B3%E1%86%A8%E1%84%8B%E1%85%B4%20%E1%84%87%E1%85%B5%E1%84%86%E1%85%B5%E1%86%AF%20HyperCLOVA,%20%E1%84%87%E1%85%B5%E1%86%A8%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%E1%84%8B%E1%85%A6%E1%84%89%E1%85%A5%20%E1%84%8B%E1%85%A8%E1%84%8E%E1%85%B3%E1%86%A8%20%E1%84%89%E1%85%B5%E1%84%80%E1%85%B3%E1%84%82%E1%85%A5%E1%86%AF%E1%84%8B%E1%85%B3%E1%86%AF%20%E1%84%8E%E1%85%A1%E1%86%BD%E1%84%8B%E1%85%B3%E1%86%AF%20%E1%84%89%E1%85%AE%20%E1%84%8B%E1%85%B5%E1%86%BB%E1%84%82%E1%85%B3%E1%86%AB%20%E1%84%8B%E1%85%B5%E1%84%8B%E1%85%B2.pdf)
