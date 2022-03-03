---
layout: single
title: "아마존닷컴의 개인화 및 추천기능을 우리도 써봅시다 - 심호진(11번가) 강의 공부 및 정리"
date: "2022-03-03 15:47:11 +0900"
last_modified_at: "2022-03-03 15:47:11 +0900"
---


# 아마존닷컴의 개인화 및 추천기능을 우리도 써봅시다 - 심호진(11번가) 강의 공부 및 정리

## 사진 출처 : [https://www.youtube.com/watch?v=E6xKrPd43z4](https://www.youtube.com/watch?v=E6xKrPd43z4)




- Amazon Web Services Korea


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_143037_3.png")
```




![output_2_0]({{ site.gdrive_url_prefix }}1XC4NYRdAVG8NcokfsV9Wv-qdHpVYMIAC)



> Amazon forecast 과정 

1. 잘 가공된 csv 데이터 파일을 S3에 업로드 하기 

2. 알고리즘 선택 

3. 모델별 정확도 비교

4. 모델 배포 

5. Forecast 생성 ( 태블로에 적용 후 비즈니스 자동화 시스템과 연동)

## Data 준비하기 

1. Target time-series ( 예측하려는 대상 아이템의 과거 시계열 데이터)

2. Related time-series ( 아이템과 연관된 데이터 , 가격 , 클릭수 등)

3. Item metadata (카테고리 , 브랜드 등)


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_143037_1.png")
```



![output_5_0]({{ site.gdrive_url_prefix }}1tMvBP5gu7naPGESq--iKSPpoIYEEZkAe)

    



## Recipe 선택하기 

다음과 같은 알고리즘들을 선택할 수 있다.

1. ARIMA

2. SQF 

3. DeepAR+

4. ETS


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_143037_2.png")
```




![output_7_0]({{ site.gdrive_url_prefix }}10wXMKQft8pTt4wJ7aCFdlLCnrwSdxLAB)
    



## Create dataset group

1. 잘 정제된 csv 파일을 S3에 업로드 한다

2. 데이터 종류에 따라 Domain 지정한다. ( 데이터가 물류, 상품판매, 전기수요량등 특정 분야 데이터의 종류에 따라 다르게 지정해야한다.)

RETAIL Domain – For retail demand forecasting

INVENTORY_PLANNING Domain – For supply chain and inventory planning

EC2 CAPACITY Domain – For forecasting Amazon Elastic Compute Cloud (Amazon EC2) capacity

WORK_FORCE Domain – For work force planning

WEB_TRAFFIC Domain – For estimating future web traffic

METRICS Domain – For forecasting metrics, such as revenue and cash flow

CUSTOM Domain – For all other types of time-series forecasting

- 출처 : [https://docs.aws.amazon.com/forecast/latest/dg/howitworks-domains-ds-types.html](https://docs.aws.amazon.com/forecast/latest/dg/howitworks-domains-ds-types.html)




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_143851_1.png")
```




![output_9_0]({{ site.gdrive_url_prefix }}1lOG3TiKoPZPRjI_7vfc5nuGRRcc7wS3o)
    
    



## Target time series data formatting 

- data entry frequency : 밑에 사진 데이터는 1시간 빈도여서 1시간으로 지정해줌 (하루, 한달 지정으로 해줄 수도 있다.)


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_144954_1.png")
```



![output_11_0]({{ site.gdrive_url_prefix }}1IRi1QeQ43lIhLOXznZ2u3AAGw5jrK1dy)
    



## Data schema 

데이터베이스의 구조와 제약조건에 관한 전반적인 명세를 기술한다.

데이터베이스를 구성하는 데이터 개체(Entity), 속성(Attribute), 관계(Relationship) 및 데이터 조작 시 데이터 값들이 갖는 제약조건 등에 관한 전반적인 내용을 정의한다.

- 밑에 그림은 AttributeName을 timestamp, target_value, item_id 총 3가지로 지정해 주었다. 


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_144954_2.png")
```



![output_14_0]({{ site.gdrive_url_prefix }}1OYjnQY2wHSzOwCEXusgDCkRQ7VFl8QrU)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_150510_1.png")
```




![output_15_0]({{ site.gdrive_url_prefix }}1EUO2byXiGkj6GQAye74W2sTbtQNqTeus)




> 마지막에 Data location 지정해줌

## Train predictor 

1. Recipe 선택 - AutoML 선택 또는 수동으로 내가 직접 알고리즘을 선택 할 수 있다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_150822_1.png")
```




![output_18_0]({{ site.gdrive_url_prefix }}145BhmlIK4tZohCzpZCDO9D3p_pfzprc2)




## Predictor 생성 완료


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_151235_1.png")
```



![output_20_0]({{ site.gdrive_url_prefix }}1LHq5bxWB7qY8Utryz5LmISY6Mik-Xeva)
    



## 본격적으로 Forecast 해보기

1. Item identifier 설정 ( 아래 그림은 고객 엔티티로 설정해줌)

2. 예측 범위 선택 


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_151457_1.png")
```



![output_22_0]({{ site.gdrive_url_prefix }}16uDtu7yNSYQ7xE36WFHC3gP8qKsdYCoW)

    
    



## 시각화 기능 - 바로 확인 가능(장점)



```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_151824_1.png")
```



![output_24_0]({{ site.gdrive_url_prefix }}1kKGDzuxfZJ5B9PzfDEadrG86c89vaWWr)

   
    



> 왼쪽은 과거 데이터 , 오른쪽은 미래 예측 데이터이다. 50% 신뢰구간, 90% 신뢰구간으로 쉽게 데이터를 파악 가능하다
