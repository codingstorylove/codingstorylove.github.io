---
layout: single
title: "데이터스케일링과 범주특성의 변환"
date: "2022-01-24 16:27:00 +0900"
last_modified_at: "2022-01-24 16:27:10 +0900"
---

# 데이터스케일링과 범주특성의 변환

## 데이터 스케일링

- 스케일링이란 연속형 특성의 단위가 다를 경우 이로 인해 과대 혹은 과소한 파라미터가 추정될 수 있기 때문에 모든 자료에 대해서 동일한 기준으로 자료를 변환하는 것을 의미한다.

- 머신러닝/딥러닝 전에 특성변수의 스케일링 과정은 매우 중요하다

> 1. Min-Max Scaling

>> - 가장 대표적인 머신러닝/딥러닝의 스케일링 방법

>> - 각 특성변수의 값과 최소값의 차이를 (최대-최소)로 나눔

>> - 이 경우 모든 값은 0 이상의 양(+)의 값을 가짐

>> - 이상치에 영향이 있음



![sca1]({{ site.gdrive_url_prefix }}1qYRZzHGJ0hV9qCoVvN3tTunV4GvJMU4I)

> 2. standardization

>> - 평균이 0, 표준편차가 1이 되는 통계적인 자료 표준화의 대표적 값

>> - 표준화를 하는 이유는?

>>>머신러닝에서 사용하는 Support Vector Machine, Linear Regression, Logistic Regression 모델은 데이터가 가우시안 분포를 가지고 있다고 가정하여 구현되어 있어서 사전에 학습 데이터에 관해 표준화를 적용하는 것이 모델의 예측 성능 향상에 중요하다.

## 범주특성의 원핫인코딩 변환

- 머신러닝에서는 one-hot-encoding을 해줘야 함

- 더미변수로 만들어 준다. (0,1 로 구성)

> 범주형 컬럼은 원핫인코딩으로 0,1으로 구성되게 변환해줌

> 연속형 컬럼은 Min-Max Scaling 또는 standardization으로 변환해줌

- Min-Max Scaling ,standardization 둘다 해봐서 둘 중에 더 정확한 것을 선택해야 한다


# 실습

## 1. 데이터 불러오기 & 범주/연속/레이블 분류



```R
data<-read.csv("data/vote.csv", header=T)
```


```R
head(data)
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>region</th><th scope=col>edu</th><th scope=col>income</th><th scope=col>age</th><th scope=col>score_gov</th><th scope=col>score_progress</th><th scope=col>score_intention</th><th scope=col>vote</th><th scope=col>parties</th></tr></thead>
<tbody>
	<tr><td>1  </td><td>4  </td><td>3  </td><td>3  </td><td>3  </td><td>2  </td><td>2  </td><td>4.0</td><td>1  </td><td>2  </td></tr>
	<tr><td>1  </td><td>5  </td><td>2  </td><td>3  </td><td>3  </td><td>2  </td><td>4  </td><td>3.0</td><td>0  </td><td>3  </td></tr>
	<tr><td>1  </td><td>3  </td><td>1  </td><td>2  </td><td>4  </td><td>1  </td><td>3  </td><td>2.8</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>1  </td><td>3  </td><td>5  </td><td>4  </td><td>2.6</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>3  </td><td>2.4</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>4  </td><td>1  </td><td>4  </td><td>3.8</td><td>1  </td><td>2  </td></tr>
</tbody>
</table>




```R
data
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>region</th><th scope=col>edu</th><th scope=col>income</th><th scope=col>age</th><th scope=col>score_gov</th><th scope=col>score_progress</th><th scope=col>score_intention</th><th scope=col>vote</th><th scope=col>parties</th></tr></thead>
<tbody>
	<tr><td>1  </td><td>4  </td><td>3  </td><td>3  </td><td>3  </td><td>2  </td><td>2  </td><td>4.0</td><td>1  </td><td>2  </td></tr>
	<tr><td>1  </td><td>5  </td><td>2  </td><td>3  </td><td>3  </td><td>2  </td><td>4  </td><td>3.0</td><td>0  </td><td>3  </td></tr>
	<tr><td>1  </td><td>3  </td><td>1  </td><td>2  </td><td>4  </td><td>1  </td><td>3  </td><td>2.8</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>1  </td><td>3  </td><td>5  </td><td>4  </td><td>2.6</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>3  </td><td>2.4</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>4  </td><td>1  </td><td>4  </td><td>3.8</td><td>1  </td><td>2  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>4  </td><td>2.0</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>5  </td><td>2  </td><td>4  </td><td>4  </td><td>3  </td><td>4  </td><td>3.6</td><td>1  </td><td>3  </td></tr>
	<tr><td>1  </td><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>2  </td><td>2  </td><td>2.0</td><td>0  </td><td>2  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>4  </td><td>2  </td><td>3.0</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>2  </td><td>4  </td><td>2.2</td><td>0  </td><td>2  </td></tr>
	<tr><td>2  </td><td>4  </td><td>1  </td><td>1  </td><td>3  </td><td>3  </td><td>2  </td><td>2.6</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>5  </td><td>1  </td><td>2  </td><td>4  </td><td>3  </td><td>2  </td><td>3.0</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>2  </td><td>2  </td><td>4  </td><td>4  </td><td>3  </td><td>3  </td><td>2.4</td><td>1  </td><td>3  </td></tr>
	<tr><td>1  </td><td>4  </td><td>3  </td><td>4  </td><td>3  </td><td>3  </td><td>4  </td><td>3.6</td><td>1  </td><td>3  </td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>3  </td><td>3  </td><td>3  </td><td>3.2</td><td>1  </td><td>4  </td></tr>
	<tr><td>1  </td><td>5  </td><td>2  </td><td>4  </td><td>3  </td><td>4  </td><td>3  </td><td>4.0</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>2  </td><td>3  </td><td>5  </td><td>4  </td><td>2.6</td><td>1  </td><td>1  </td></tr>
	<tr><td>2  </td><td>3  </td><td>1  </td><td>2  </td><td>2  </td><td>3  </td><td>2  </td><td>3.0</td><td>0  </td><td>4  </td></tr>
	<tr><td>2  </td><td>5  </td><td>3  </td><td>4  </td><td>3  </td><td>3  </td><td>3  </td><td>3.0</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>3  </td><td>2.0</td><td>0  </td><td>2  </td></tr>
	<tr><td>2  </td><td>1  </td><td>3  </td><td>2  </td><td>2  </td><td>3  </td><td>4  </td><td>2.2</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>4  </td><td>2  </td><td>2  </td><td>2  </td><td>3  </td><td>3  </td><td>1.4</td><td>1  </td><td>4  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>3  </td><td>3  </td><td>3  </td><td>4  </td><td>1.6</td><td>1  </td><td>4  </td></tr>
	<tr><td>1  </td><td>4  </td><td>2  </td><td>3  </td><td>3  </td><td>3  </td><td>2  </td><td>3.6</td><td>1  </td><td>2  </td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>2  </td><td>2  </td><td>3  </td><td>2  </td><td>3.2</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>3  </td><td>2  </td><td>3  </td><td>4  </td><td>2.8</td><td>1  </td><td>1  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>3  </td><td>4  </td><td>4  </td><td>3.0</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>3  </td><td>3  </td><td>1  </td><td>2  </td><td>3.0</td><td>1  </td><td>2  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>3  </td><td>3  </td><td>2.2</td><td>1  </td><td>3  </td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>1  </td><td>2  </td><td>3  </td><td>5  </td><td>2.2</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>2  </td><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>3  </td><td>3.4</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>1  </td><td>1  </td><td>1  </td><td>4  </td><td>2  </td><td>3.4</td><td>1  </td><td>4  </td></tr>
	<tr><td>1  </td><td>4  </td><td>1  </td><td>1  </td><td>2  </td><td>1  </td><td>4  </td><td>2.8</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>1  </td><td>1  </td><td>1  </td><td>3  </td><td>4  </td><td>2.8</td><td>1  </td><td>2  </td></tr>
	<tr><td>2  </td><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>2  </td><td>4  </td><td>3.0</td><td>0  </td><td>4  </td></tr>
	<tr><td>1  </td><td>5  </td><td>2  </td><td>2  </td><td>3  </td><td>4  </td><td>3  </td><td>3.4</td><td>0  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>1  </td><td>1  </td><td>3  </td><td>3  </td><td>2.8</td><td>1  </td><td>4  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>3  </td><td>3.0</td><td>1  </td><td>4  </td></tr>
	<tr><td>1  </td><td>5  </td><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>2  </td><td>2.6</td><td>0  </td><td>2  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>1  </td><td>3  </td><td>4  </td><td>4  </td><td>4.4</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>2  </td><td>1  </td><td>1  </td><td>1  </td><td>3  </td><td>4  </td><td>2.8</td><td>0  </td><td>4  </td></tr>
	<tr><td>1  </td><td>5  </td><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>4  </td><td>3.4</td><td>1  </td><td>4  </td></tr>
	<tr><td>1  </td><td>2  </td><td>2  </td><td>1  </td><td>2  </td><td>2  </td><td>1  </td><td>2.2</td><td>1  </td><td>2  </td></tr>
	<tr><td>1  </td><td>5  </td><td>1  </td><td>1  </td><td>1  </td><td>3  </td><td>3  </td><td>3.0</td><td>0  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>2  </td><td>3  </td><td>2  </td><td>4  </td><td>3.0</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>1  </td><td>1  </td><td>3  </td><td>2  </td><td>3  </td><td>3.0</td><td>1  </td><td>4  </td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>5.0</td><td>1  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>2  </td><td>2.2</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>3  </td><td>3.0</td><td>1  </td><td>1  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>3.6</td><td>0  </td><td>1  </td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>1  </td><td>2  </td><td>3  </td><td>3  </td><td>3.4</td><td>1  </td><td>1  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>2  </td><td>2  </td><td>3  </td><td>3  </td><td>3.6</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>1  </td><td>1  </td><td>1  </td><td>5  </td><td>4  </td><td>3.2</td><td>0  </td><td>1  </td></tr>
	<tr><td>2  </td><td>1  </td><td>1  </td><td>3  </td><td>4  </td><td>3  </td><td>2  </td><td>1.0</td><td>1  </td><td>2  </td></tr>
	<tr><td>1  </td><td>4  </td><td>1  </td><td>4  </td><td>4  </td><td>3  </td><td>3  </td><td>1.8</td><td>1  </td><td>2  </td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>1  </td><td>2  </td><td>3  </td><td>4  </td><td>2.6</td><td>1  </td><td>4  </td></tr>
	<tr><td>1  </td><td>2  </td><td>2  </td><td>1  </td><td>2  </td><td>3  </td><td>3  </td><td>2.6</td><td>1  </td><td>2  </td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>4  </td><td>3  </td><td>2  </td><td>4.0</td><td>1  </td><td>4  </td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>2  </td><td>2  </td><td>3  </td><td>3  </td><td>3.8</td><td>1  </td><td>2  </td></tr>
</tbody>
</table>



### 범주형 자료 따로 분리해주기


```R
data_cat <-subset(data, select=c(gender, region))
```


```R
data_cat
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>region</th></tr></thead>
<tbody>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>5</td></tr>
	<tr><td>1</td><td>3</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>5</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>2</td><td>4</td></tr>
	<tr><td>1</td><td>5</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>5</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>2</td><td>3</td></tr>
	<tr><td>2</td><td>5</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>2</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>...</td><td>...</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>1</td><td>5</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>5</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>2</td><td>2</td></tr>
	<tr><td>1</td><td>5</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>5</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>2</td><td>1</td></tr>
</tbody>
</table>



### 연속형 자료 따로 분리해주기


```R
data_num <-subset(data, select = c(edu, income, age, score_gov, score_progress, score_intention))
```


```R
data_num
```


<table>
<thead><tr><th scope=col>edu</th><th scope=col>income</th><th scope=col>age</th><th scope=col>score_gov</th><th scope=col>score_progress</th><th scope=col>score_intention</th></tr></thead>
<tbody>
	<tr><td>3  </td><td>3  </td><td>3  </td><td>2  </td><td>2  </td><td>4.0</td></tr>
	<tr><td>2  </td><td>3  </td><td>3  </td><td>2  </td><td>4  </td><td>3.0</td></tr>
	<tr><td>1  </td><td>2  </td><td>4  </td><td>1  </td><td>3  </td><td>2.8</td></tr>
	<tr><td>2  </td><td>1  </td><td>3  </td><td>5  </td><td>4  </td><td>2.6</td></tr>
	<tr><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>3  </td><td>2.4</td></tr>
	<tr><td>1  </td><td>2  </td><td>4  </td><td>1  </td><td>4  </td><td>3.8</td></tr>
	<tr><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>4  </td><td>2.0</td></tr>
	<tr><td>2  </td><td>4  </td><td>4  </td><td>3  </td><td>4  </td><td>3.6</td></tr>
	<tr><td>1  </td><td>2  </td><td>4  </td><td>2  </td><td>2  </td><td>2.0</td></tr>
	<tr><td>1  </td><td>2  </td><td>3  </td><td>4  </td><td>2  </td><td>3.0</td></tr>
	<tr><td>1  </td><td>2  </td><td>3  </td><td>2  </td><td>4  </td><td>2.2</td></tr>
	<tr><td>1  </td><td>1  </td><td>3  </td><td>3  </td><td>2  </td><td>2.6</td></tr>
	<tr><td>1  </td><td>2  </td><td>4  </td><td>3  </td><td>2  </td><td>3.0</td></tr>
	<tr><td>2  </td><td>4  </td><td>4  </td><td>3  </td><td>3  </td><td>2.4</td></tr>
	<tr><td>3  </td><td>4  </td><td>3  </td><td>3  </td><td>4  </td><td>3.6</td></tr>
	<tr><td>2  </td><td>3  </td><td>3  </td><td>3  </td><td>3  </td><td>3.2</td></tr>
	<tr><td>2  </td><td>4  </td><td>3  </td><td>4  </td><td>3  </td><td>4.0</td></tr>
	<tr><td>2  </td><td>2  </td><td>3  </td><td>5  </td><td>4  </td><td>2.6</td></tr>
	<tr><td>1  </td><td>2  </td><td>2  </td><td>3  </td><td>2  </td><td>3.0</td></tr>
	<tr><td>3  </td><td>4  </td><td>3  </td><td>3  </td><td>3  </td><td>3.0</td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>3  </td><td>2.0</td></tr>
	<tr><td>3  </td><td>2  </td><td>2  </td><td>3  </td><td>4  </td><td>2.2</td></tr>
	<tr><td>2  </td><td>2  </td><td>2  </td><td>3  </td><td>3  </td><td>1.4</td></tr>
	<tr><td>1  </td><td>3  </td><td>3  </td><td>3  </td><td>4  </td><td>1.6</td></tr>
	<tr><td>2  </td><td>3  </td><td>3  </td><td>3  </td><td>2  </td><td>3.6</td></tr>
	<tr><td>2  </td><td>2  </td><td>2  </td><td>3  </td><td>2  </td><td>3.2</td></tr>
	<tr><td>2  </td><td>3  </td><td>2  </td><td>3  </td><td>4  </td><td>2.8</td></tr>
	<tr><td>2  </td><td>4  </td><td>3  </td><td>4  </td><td>4  </td><td>3.0</td></tr>
	<tr><td>1  </td><td>3  </td><td>3  </td><td>1  </td><td>2  </td><td>3.0</td></tr>
	<tr><td>2  </td><td>4  </td><td>4  </td><td>3  </td><td>3  </td><td>2.2</td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>3  </td><td>5  </td><td>2.2</td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>3  </td><td>3.4</td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>4  </td><td>2  </td><td>3.4</td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>1  </td><td>4  </td><td>2.8</td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>3  </td><td>4  </td><td>2.8</td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>2  </td><td>4  </td><td>3.0</td></tr>
	<tr><td>2  </td><td>2  </td><td>3  </td><td>4  </td><td>3  </td><td>3.4</td></tr>
	<tr><td>2  </td><td>1  </td><td>1  </td><td>3  </td><td>3  </td><td>2.8</td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>3  </td><td>3.0</td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>2  </td><td>2  </td><td>2.6</td></tr>
	<tr><td>2  </td><td>1  </td><td>3  </td><td>4  </td><td>4  </td><td>4.4</td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>3  </td><td>4  </td><td>2.8</td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>4  </td><td>3.4</td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>2  </td><td>1  </td><td>2.2</td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>3  </td><td>3  </td><td>3.0</td></tr>
	<tr><td>2  </td><td>2  </td><td>3  </td><td>2  </td><td>4  </td><td>3.0</td></tr>
	<tr><td>1  </td><td>1  </td><td>3  </td><td>2  </td><td>3  </td><td>3.0</td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>5.0</td></tr>
	<tr><td>1  </td><td>1  </td><td>2  </td><td>3  </td><td>2  </td><td>2.2</td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>3  </td><td>3.0</td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>4  </td><td>4  </td><td>3.6</td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>3  </td><td>3  </td><td>3.4</td></tr>
	<tr><td>2  </td><td>2  </td><td>2  </td><td>3  </td><td>3  </td><td>3.6</td></tr>
	<tr><td>1  </td><td>1  </td><td>1  </td><td>5  </td><td>4  </td><td>3.2</td></tr>
	<tr><td>1  </td><td>3  </td><td>4  </td><td>3  </td><td>2  </td><td>1.0</td></tr>
	<tr><td>1  </td><td>4  </td><td>4  </td><td>3  </td><td>3  </td><td>1.8</td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>3  </td><td>4  </td><td>2.6</td></tr>
	<tr><td>2  </td><td>1  </td><td>2  </td><td>3  </td><td>3  </td><td>2.6</td></tr>
	<tr><td>2  </td><td>3  </td><td>4  </td><td>3  </td><td>2  </td><td>4.0</td></tr>
	<tr><td>2  </td><td>2  </td><td>2  </td><td>3  </td><td>3  </td><td>3.8</td></tr>
</tbody>
</table>



### 레이블 데이터 분리해주기




```R
data_class<-subset(data, select=c(vote, parties))
```


```R
data_class
```


<table>
<thead><tr><th scope=col>vote</th><th scope=col>parties</th></tr></thead>
<tbody>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>0</td><td>3</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>3</td></tr>
	<tr><td>0</td><td>2</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>0</td><td>2</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>3</td></tr>
	<tr><td>1</td><td>3</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>0</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>0</td><td>2</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>3</td></tr>
	<tr><td>...</td><td>...</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>0</td><td>4</td></tr>
	<tr><td>0</td><td>1</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>0</td><td>2</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>0</td><td>4</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>0</td><td>4</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>0</td><td>1</td></tr>
	<tr><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>0</td><td>1</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>4</td></tr>
	<tr><td>1</td><td>2</td></tr>
</tbody>
</table>



## 2. 범주형 특성의 웟핫인코딩(one-hot-encoding)



```R
data_cat$gender<-factor(data_cat$gender, labels=c("male", "female"))
str(data_cat)
```

    'data.frame':	211 obs. of  2 variables:
     $ gender: Factor w/ 2 levels "male","female": 1 1 1 2 1 1 1 1 1 1 ...
     $ region: int  4 5 3 1 1 1 1 5 2 1 ...



```R
data_cat$region<-factor(data_cat$region, labels=c('Sudo', 'Chungcheung', 'Honam', 'Youngnam', 'Others'))
str(data_cat)
```

    'data.frame':	211 obs. of  2 variables:
     $ gender: Factor w/ 2 levels "male","female": 1 1 1 2 1 1 1 1 1 1 ...
     $ region: Factor w/ 5 levels "Sudo","Chungcheung",..: 4 5 3 1 1 1 1 5 2 1 ...



```R
data_cat
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>region</th></tr></thead>
<tbody>
	<tr><td>male       </td><td>Youngnam   </td></tr>
	<tr><td>male       </td><td>Others     </td></tr>
	<tr><td>male       </td><td>Honam      </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Others     </td></tr>
	<tr><td>male       </td><td>Chungcheung</td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Youngnam   </td></tr>
	<tr><td>male       </td><td>Others     </td></tr>
	<tr><td>male       </td><td>Chungcheung</td></tr>
	<tr><td>male       </td><td>Youngnam   </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Others     </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Honam      </td></tr>
	<tr><td>female     </td><td>Others     </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Youngnam   </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Youngnam   </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>...</td><td>...</td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Chungcheung</td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Youngnam   </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Others     </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Others     </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Chungcheung</td></tr>
	<tr><td>male       </td><td>Others     </td></tr>
	<tr><td>male       </td><td>Chungcheung</td></tr>
	<tr><td>male       </td><td>Others     </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Youngnam   </td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>male       </td><td>Chungcheung</td></tr>
	<tr><td>male       </td><td>Sudo       </td></tr>
	<tr><td>female     </td><td>Sudo       </td></tr>
</tbody>
</table>




```R

install.packages("caret")
library(caret)


```


      There is a binary version available but the source version is later:
          binary source needs_compilation
    caret 6.0-86 6.0-90              TRUE

      Binaries will be installed
    package 'caret' successfully unpacked and MD5 sums checked

    The downloaded binary packages are in
    	C:\Users\MyCom\AppData\Local\Temp\RtmpEDGEx8\downloaded_packages


    Warning message:
    "package 'caret' was built under R version 3.6.3"Loading required package: lattice
    Loading required package: ggplot2



```R
one_hot <- dummyVars(" ~ .", data = data_cat)
one_hot
```


    Dummy Variable Object

    Formula: ~.
    <environment: 0x00000000691091c0>
    2 variables, 2 factors
    Variables and levels will be separated by '.'
    A less than full rank encoding is used


> 데이터 프레임으로 바꿔주기


```R
data_cat2 <- data.frame(predict(one_hot, newdata = data_cat))
head(data_cat2)
```


<table>
<thead><tr><th scope=col>gender.male</th><th scope=col>gender.female</th><th scope=col>region.Sudo</th><th scope=col>region.Chungcheung</th><th scope=col>region.Honam</th><th scope=col>region.Youngnam</th><th scope=col>region.Others</th></tr></thead>
<tbody>
	<tr><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>1</td><td>0</td></tr>
	<tr><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>1</td></tr>
	<tr><td>1</td><td>0</td><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td></tr>
	<tr><td>0</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td></tr>
	<tr><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td></tr>
	<tr><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td></tr>
</tbody>
</table>



## 3. 연속형 특성의 Scaling

### 3-1. 표준화(평균 0, 표준편차 1) scaling

> 연속형 컬럼은 Min-Max Scaling 또는 standardization으로 변환해줌

- Min-Max Scaling ,standardization 둘다 해봐서 둘 중에 더 정확한 것을 선택해야 한다



```R
library(caret)
StandardScale <- preProcess(data_num, method=c("center", "scale"))
print(StandardScale)
```

    Created from 211 samples and 6 variables

    Pre-processing:
      - centered (6)
      - ignored (0)
      - scaled (6)



- 데이터 프레임으로 만들어주기


```R
data_standard <- predict(StandardScale, data_num)
head(data_standard)
```


<table>
<thead><tr><th scope=col>edu</th><th scope=col>income</th><th scope=col>age</th><th scope=col>score_gov</th><th scope=col>score_progress</th><th scope=col>score_intention</th></tr></thead>
<tbody>
	<tr><td> 1.8095327 </td><td> 0.7421712 </td><td>0.3966776  </td><td>-1.1190329 </td><td>-1.13873228</td><td> 1.5020451 </td></tr>
	<tr><td> 0.2119955 </td><td> 0.7421712 </td><td>0.3966776  </td><td>-1.1190329 </td><td> 0.94154920</td><td> 0.1228827 </td></tr>
	<tr><td>-1.3855418 </td><td>-0.1955421 </td><td>1.5432389  </td><td>-2.1778487 </td><td>-0.09859154</td><td>-0.1529498 </td></tr>
	<tr><td> 0.2119955 </td><td>-1.1332554 </td><td>0.3966776  </td><td> 2.0574147 </td><td> 0.94154920</td><td>-0.4287823 </td></tr>
	<tr><td>-1.3855418 </td><td>-0.1955421 </td><td>1.5432389  </td><td> 0.9985988 </td><td>-0.09859154</td><td>-0.7046147 </td></tr>
	<tr><td>-1.3855418 </td><td>-0.1955421 </td><td>1.5432389  </td><td>-2.1778487 </td><td> 0.94154920</td><td> 1.2262127 </td></tr>
</tbody>
</table>



### 3-2. min-max scaling



```R
MinMaxScale <- preProcess(data_num, method=c("range"))
print(MinMaxScale)


```

    Created from 211 samples and 6 variables

    Pre-processing:
      - ignored (0)
      - re-scaling to [0, 1] (6)




```R
data_minmax <- predict(MinMaxScale, data_num)
head(data_minmax)
```


<table>
<thead><tr><th scope=col>edu</th><th scope=col>income</th><th scope=col>age</th><th scope=col>score_gov</th><th scope=col>score_progress</th><th scope=col>score_intention</th></tr></thead>
<tbody>
	<tr><td>1.0      </td><td>0.6666667</td><td>0.6666667</td><td>0.25     </td><td>0.25     </td><td>0.75     </td></tr>
	<tr><td>0.5      </td><td>0.6666667</td><td>0.6666667</td><td>0.25     </td><td>0.75     </td><td>0.50     </td></tr>
	<tr><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.00     </td><td>0.50     </td><td>0.45     </td></tr>
	<tr><td>0.5      </td><td>0.0000000</td><td>0.6666667</td><td>1.00     </td><td>0.75     </td><td>0.40     </td></tr>
	<tr><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.75     </td><td>0.50     </td><td>0.35     </td></tr>
	<tr><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.00     </td><td>0.75     </td><td>0.70     </td></tr>
</tbody>
</table>



## 4. 데이터 통합 및 저장


```R
# cbind로 종 데이터를 추가해준다. cbind 외에도 여러가지 방법으로 같은 작업이 가능하다
Fvote = cbind(data_cat2, data_standard, data_class)
Fvote
```


<table>
<thead><tr><th scope=col>gender.male</th><th scope=col>gender.female</th><th scope=col>region.Sudo</th><th scope=col>region.Chungcheung</th><th scope=col>region.Honam</th><th scope=col>region.Youngnam</th><th scope=col>region.Others</th><th scope=col>edu</th><th scope=col>income</th><th scope=col>age</th><th scope=col>score_gov</th><th scope=col>score_progress</th><th scope=col>score_intention</th><th scope=col>vote</th><th scope=col>parties</th></tr></thead>
<tbody>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td> 1.8095327 </td><td> 0.7421712 </td><td> 0.3966776 </td><td>-1.11903285</td><td>-1.13873228</td><td> 1.5020451 </td><td>1          </td><td>2          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td> 0.2119955 </td><td> 0.7421712 </td><td> 0.3966776 </td><td>-1.11903285</td><td> 0.94154920</td><td> 0.1228827 </td><td>0          </td><td>3          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-0.1955421 </td><td> 1.5432389 </td><td>-2.17784869</td><td>-0.09859154</td><td>-0.1529498 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td> 0.3966776 </td><td> 2.05741467</td><td> 0.94154920</td><td>-0.4287823 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-0.1955421 </td><td> 1.5432389 </td><td> 0.99859883</td><td>-0.09859154</td><td>-0.7046147 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-0.1955421 </td><td> 1.5432389 </td><td>-2.17784869</td><td> 0.94154920</td><td> 1.2262127 </td><td>1          </td><td>2          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-0.1955421 </td><td> 1.5432389 </td><td> 0.99859883</td><td> 0.94154920</td><td>-1.2562797 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td> 0.2119955 </td><td> 1.6798845 </td><td> 1.5432389 </td><td>-0.06021701</td><td> 0.94154920</td><td> 0.9503802 </td><td>1          </td><td>3          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-0.1955421 </td><td> 1.5432389 </td><td>-1.11903285</td><td>-1.13873228</td><td>-1.2562797 </td><td>0          </td><td>2          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-0.1955421 </td><td> 0.3966776 </td><td> 0.99859883</td><td>-1.13873228</td><td> 0.1228827 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-0.1955421 </td><td> 0.3966776 </td><td>-1.11903285</td><td> 0.94154920</td><td>-0.9804472 </td><td>0          </td><td>2          </td></tr>
	<tr><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td> 0.3966776 </td><td>-0.06021701</td><td>-1.13873228</td><td>-0.4287823 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>-1.3855418 </td><td>-0.1955421 </td><td> 1.5432389 </td><td>-0.06021701</td><td>-1.13873228</td><td> 0.1228827 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td> 1.6798845 </td><td> 1.5432389 </td><td>-0.06021701</td><td>-0.09859154</td><td>-0.7046147 </td><td>1          </td><td>3          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td> 1.8095327 </td><td> 1.6798845 </td><td> 0.3966776 </td><td>-0.06021701</td><td> 0.94154920</td><td> 0.9503802 </td><td>1          </td><td>3          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td> 0.7421712 </td><td> 0.3966776 </td><td>-0.06021701</td><td>-0.09859154</td><td> 0.3987152 </td><td>1          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td> 0.2119955 </td><td> 1.6798845 </td><td> 0.3966776 </td><td> 0.99859883</td><td>-0.09859154</td><td> 1.5020451 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-0.1955421 </td><td> 0.3966776 </td><td> 2.05741467</td><td> 0.94154920</td><td>-0.4287823 </td><td>1          </td><td>1          </td></tr>
	<tr><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-0.1955421 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-1.13873228</td><td> 0.1228827 </td><td>0          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td> 1.8095327 </td><td> 1.6798845 </td><td> 0.3966776 </td><td>-0.06021701</td><td>-0.09859154</td><td> 0.1228827 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-0.09859154</td><td>-1.2562797 </td><td>0          </td><td>2          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 1.8095327 </td><td>-0.1955421 </td><td>-0.7498837 </td><td>-0.06021701</td><td> 0.94154920</td><td>-0.9804472 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td> 0.2119955 </td><td>-0.1955421 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-0.09859154</td><td>-2.0837772 </td><td>1          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td> 0.7421712 </td><td> 0.3966776 </td><td>-0.06021701</td><td> 0.94154920</td><td>-1.8079447 </td><td>1          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td> 0.2119955 </td><td> 0.7421712 </td><td> 0.3966776 </td><td>-0.06021701</td><td>-1.13873228</td><td> 0.9503802 </td><td>1          </td><td>2          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-0.1955421 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-1.13873228</td><td> 0.3987152 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td> 0.7421712 </td><td>-0.7498837 </td><td>-0.06021701</td><td> 0.94154920</td><td>-0.1529498 </td><td>1          </td><td>1          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td> 1.6798845 </td><td> 0.3966776 </td><td> 0.99859883</td><td> 0.94154920</td><td> 0.1228827 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td> 0.7421712 </td><td> 0.3966776 </td><td>-2.17784869</td><td>-1.13873228</td><td> 0.1228827 </td><td>1          </td><td>2          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td> 1.6798845 </td><td> 1.5432389 </td><td>-0.06021701</td><td>-0.09859154</td><td>-0.9804472 </td><td>1          </td><td>3          </td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-0.06021701</td><td> 1.98168994</td><td>-0.9804472 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-0.7498837 </td><td> 0.99859883</td><td>-0.09859154</td><td> 0.6745477 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-1.8964449 </td><td> 0.99859883</td><td>-1.13873228</td><td> 0.6745477 </td><td>1          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-2.17784869</td><td> 0.94154920</td><td>-0.1529498 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-1.8964449 </td><td>-0.06021701</td><td> 0.94154920</td><td>-0.1529498 </td><td>1          </td><td>2          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-1.11903285</td><td> 0.94154920</td><td> 0.1228827 </td><td>0          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td> 0.2119955 </td><td>-0.1955421 </td><td> 0.3966776 </td><td> 0.99859883</td><td>-0.09859154</td><td> 0.6745477 </td><td>0          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-1.8964449 </td><td>-0.06021701</td><td>-0.09859154</td><td>-0.1529498 </td><td>1          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-0.09859154</td><td> 0.1228827 </td><td>1          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-1.8964449 </td><td>-1.11903285</td><td>-1.13873228</td><td>-0.4287823 </td><td>0          </td><td>2          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td> 0.3966776 </td><td> 0.99859883</td><td> 0.94154920</td><td> 2.0537101 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-1.8964449 </td><td>-0.06021701</td><td> 0.94154920</td><td>-0.1529498 </td><td>0          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-0.06021701</td><td> 0.94154920</td><td> 0.6745477 </td><td>1          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-1.11903285</td><td>-2.17887302</td><td>-0.9804472 </td><td>1          </td><td>2          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-1.8964449 </td><td>-0.06021701</td><td>-0.09859154</td><td> 0.1228827 </td><td>0          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-0.1955421 </td><td> 0.3966776 </td><td>-1.11903285</td><td> 0.94154920</td><td> 0.1228827 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td> 0.3966776 </td><td>-1.11903285</td><td>-0.09859154</td><td> 0.1228827 </td><td>1          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-0.7498837 </td><td> 0.99859883</td><td> 0.94154920</td><td> 2.8812076 </td><td>1          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-1.13873228</td><td>-0.9804472 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-0.7498837 </td><td> 0.99859883</td><td>-0.09859154</td><td> 0.1228827 </td><td>1          </td><td>1          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-0.7498837 </td><td> 0.99859883</td><td> 0.94154920</td><td> 0.9503802 </td><td>0          </td><td>1          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-0.09859154</td><td> 0.6745477 </td><td>1          </td><td>1          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-0.1955421 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-0.09859154</td><td> 0.9503802 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td>-1.1332554 </td><td>-1.8964449 </td><td> 2.05741467</td><td> 0.94154920</td><td> 0.3987152 </td><td>0          </td><td>1          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>-1.3855418 </td><td> 0.7421712 </td><td> 1.5432389 </td><td>-0.06021701</td><td>-1.13873228</td><td>-2.6354421 </td><td>1          </td><td>2          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>-1.3855418 </td><td> 1.6798845 </td><td> 1.5432389 </td><td>-0.06021701</td><td>-0.09859154</td><td>-1.5321122 </td><td>1          </td><td>2          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-0.06021701</td><td> 0.94154920</td><td>-0.4287823 </td><td>1          </td><td>4          </td></tr>
	<tr><td>1          </td><td>0          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-1.1332554 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-0.09859154</td><td>-0.4287823 </td><td>1          </td><td>2          </td></tr>
	<tr><td>1          </td><td>0          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td> 0.7421712 </td><td> 1.5432389 </td><td>-0.06021701</td><td>-1.13873228</td><td> 1.5020451 </td><td>1          </td><td>4          </td></tr>
	<tr><td>0          </td><td>1          </td><td>1          </td><td>0          </td><td>0          </td><td>0          </td><td>0          </td><td> 0.2119955 </td><td>-0.1955421 </td><td>-0.7498837 </td><td>-0.06021701</td><td>-0.09859154</td><td> 1.2262127 </td><td>1          </td><td>2          </td></tr>
</tbody>
</table>




```R
write.csv(Fvote, file="Fvote2.csv", row.names=TRUE)
```
