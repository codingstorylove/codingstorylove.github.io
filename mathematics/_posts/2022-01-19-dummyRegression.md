---
layout: single
title: "더미회귀분석의 개념과 원리"
date: "2022-01-19 17:40:10 +0900"
last_modified_at: "2022-01-19 17:40:12 +0900"
---


# 더미회귀분석의 개념과 원리

# 더미변수의 개념

- 실제 사회현상에서는 종속변수에 영향을 미치는 주요한 독립변수가 범주형 자료인 경우가 많음

- 더미변수란 코딩된 숫자의 의미(예 기혼=1,미혼=2)는 없애고 특정 범주에 해당되는지 여부(미혼=1, 기혼=0)으로 변경한 변수

![dummy1]({{ site.gdrive_url_prefix }}17R01HVWGbDqh839-HHua-iyn4gvZ53UM)



- 참고사이트 : [https://m.blog.naver.com/y4769/220442966629](https://m.blog.naver.com/y4769/220442966629)

- 보건과학통계 SPSS 이야기 / 네이버블로그 참고함

- 범주형 변수를 연속형 변수로 변화는 방법

- 각 범주값을 표현하기 위해 0과 1로 코딩 변경

- 가변수라고 함


- 예

|성별|x|
|-|-|
|남|0|
|여|1|

|연령|x1|x2|x3|
|------|---|---|---|
|10대     |0|0|0|
|20대     |1|0|0|
|30대     |0|1|0|
|40대 이상|0|0|1|

# 실습과 시각화


```R
a <-read.csv('cosmetics.csv',header = T)
```


```R
a
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>marriage</th><th scope=col>edu</th><th scope=col>job</th><th scope=col>mincome</th><th scope=col>aware</th><th scope=col>count</th><th scope=col>amount</th><th scope=col>decision</th><th scope=col>propensity</th><th scope=col>skin</th><th scope=col>promo</th><th scope=col>location</th><th scope=col>satisf_b</th><th scope=col>satisf_i</th><th scope=col>satisf_al</th><th scope=col>repurchase</th></tr></thead>
<tbody>
	<tr><td>1      </td><td>1      </td><td>4      </td><td>1      </td><td>2      </td><td> 2     </td><td> 1     </td><td>  11000</td><td>2      </td><td>1      </td><td>1      </td><td>1      </td><td>2      </td><td>5      </td><td>2      </td><td>2      </td><td>2      </td></tr>
	<tr><td>2      </td><td>1      </td><td>4      </td><td>9      </td><td>2      </td><td> 1     </td><td> 4     </td><td>  30000</td><td>1      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>4      </td><td>3      </td><td> 1     </td><td> 6     </td><td> 100000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>4      </td><td>5      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>7      </td><td>5      </td><td> 2     </td><td> 6     </td><td>  65000</td><td>3      </td><td>2      </td><td>5      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>2      </td><td>6      </td><td>6      </td><td>5      </td><td> 2     </td><td> 2     </td><td>  50000</td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>2      </td><td>2      </td><td>2      </td><td>7      </td><td>3      </td><td> 1     </td><td> 2     </td><td> 100000</td><td>2      </td><td>1      </td><td>4      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>3      </td></tr>
	<tr><td>2      </td><td>1      </td><td>6      </td><td>4      </td><td>5      </td><td> 1     </td><td> 5     </td><td> 100000</td><td>3      </td><td>2      </td><td>5      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td></tr>
	<tr><td>1      </td><td>1      </td><td>6      </td><td>4      </td><td>5      </td><td> 4     </td><td>10     </td><td>  39000</td><td>3      </td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>4      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>5      </td><td>2      </td><td> 2     </td><td> 2     </td><td>  40000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>5      </td><td>2      </td><td> 1     </td><td> 2     </td><td> 100000</td><td>3      </td><td>3      </td><td>3      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>1      </td><td>7      </td><td>4      </td><td>3      </td><td>10     </td><td> 3     </td><td>  50000</td><td>1      </td><td>3      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>1      </td><td>2      </td><td>5      </td><td>3      </td><td> 2     </td><td> 1     </td><td>  30000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>4      </td><td>3      </td><td> 4     </td><td> 4     </td><td> 320000</td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>2      </td><td>4      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>4      </td><td>2      </td><td> 3     </td><td> 2     </td><td> 200000</td><td>1      </td><td>2      </td><td>3      </td><td>1      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td>4      </td><td>6      </td><td> 2     </td><td> 2     </td><td>  60000</td><td>3      </td><td>2      </td><td>1      </td><td>2      </td><td>5      </td><td>3      </td><td>3      </td><td>3      </td><td>4      </td></tr>
	<tr><td>2      </td><td>1      </td><td>4      </td><td>5      </td><td>2      </td><td> 2     </td><td> 3     </td><td>  50000</td><td>1      </td><td>2      </td><td>4      </td><td>1      </td><td>3      </td><td>3      </td><td>4      </td><td>3      </td><td>3      </td></tr>
	<tr><td>1      </td><td>2      </td><td>8      </td><td>3      </td><td>2      </td><td> 5     </td><td> 3     </td><td>1000000</td><td>1      </td><td>3      </td><td>1      </td><td>2      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>2      </td><td>1      </td><td>3      </td><td>8      </td><td>5      </td><td> 1     </td><td> 6     </td><td>1500000</td><td>3      </td><td>3      </td><td>2      </td><td>4      </td><td>1      </td><td>4      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>2      </td><td>2      </td><td>6      </td><td>2      </td><td> 4     </td><td> 1     </td><td>  80000</td><td>2      </td><td>3      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td>4      </td><td>3      </td><td> 8     </td><td> 3     </td><td>  30000</td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>2      </td><td>2      </td><td>2      </td><td>4      </td><td>2      </td><td> 8     </td><td> 4     </td><td> 350000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>7      </td><td>6      </td><td> 1     </td><td> 4     </td><td> 250000</td><td>3      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>7      </td><td>3      </td><td> 1     </td><td>25     </td><td>  50000</td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>2      </td><td>9      </td><td>1      </td><td> 1     </td><td> 1     </td><td>  20000</td><td>1      </td><td>1      </td><td>5      </td><td>1      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>1      </td><td>1      </td><td>3      </td><td>8      </td><td>4      </td><td> 2     </td><td> 3     </td><td>  42000</td><td>1      </td><td>2      </td><td>3      </td><td>1      </td><td>3      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td>8      </td><td>4      </td><td> 2     </td><td> 3     </td><td>  42000</td><td>3      </td><td>3      </td><td>2      </td><td>1      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>1      </td><td>4      </td><td>4      </td><td>3      </td><td> 2     </td><td>20     </td><td>  40000</td><td>3      </td><td>1      </td><td>5      </td><td>2      </td><td>3      </td><td>2      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>4      </td><td>6      </td><td> 1     </td><td> 6     </td><td>  70000</td><td>3      </td><td>3      </td><td>5      </td><td>2      </td><td>1      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>8      </td><td>4      </td><td>5      </td><td> 5     </td><td> 6     </td><td> 200000</td><td>2      </td><td>1      </td><td>4      </td><td>1      </td><td>1      </td><td>4      </td><td>4      </td><td>4      </td><td>3      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td>2      </td><td>6      </td><td> 2     </td><td> 1     </td><td> 200000</td><td>3      </td><td>2      </td><td>1      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 5     </td><td>2      </td><td>1      </td><td>10     </td><td>  30000</td><td>2      </td><td>2      </td><td>2      </td><td>4      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 6     </td><td>6      </td><td>7      </td><td> 5     </td><td>  50000</td><td>3      </td><td>2      </td><td>4      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 1     </td><td>4      </td><td>1      </td><td> 1     </td><td>  10000</td><td>1      </td><td>3      </td><td>1      </td><td>1      </td><td>1      </td><td>5      </td><td>1      </td><td>3      </td><td>1      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 4     </td><td>3      </td><td>2      </td><td> 1     </td><td>  10000</td><td>1      </td><td>1      </td><td>4      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 4     </td><td>3      </td><td>2      </td><td> 3     </td><td>  50000</td><td>3      </td><td>2      </td><td>5      </td><td>2      </td><td>3      </td><td>2      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>2      </td><td>2      </td><td> 5     </td><td>4      </td><td>2      </td><td> 1     </td><td>  60000</td><td>1      </td><td>1      </td><td>1      </td><td>4      </td><td>5      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>1      </td><td>1      </td><td>6      </td><td> 1     </td><td>3      </td><td>2      </td><td> 2     </td><td>  50000</td><td>3      </td><td>2      </td><td>5      </td><td>1      </td><td>2      </td><td>4      </td><td>4      </td><td>3      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>6      </td><td> 4     </td><td>6      </td><td>1      </td><td> 3     </td><td> 500000</td><td>3      </td><td>3      </td><td>5      </td><td>2      </td><td>1      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 1     </td><td>3      </td><td>2      </td><td> 1     </td><td>  50000</td><td>3      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>2      </td><td>2      </td><td>6      </td><td> 3     </td><td>4      </td><td>1      </td><td> 1     </td><td> 100000</td><td>3      </td><td>2      </td><td>1      </td><td>2      </td><td>1      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 7     </td><td>4      </td><td>1      </td><td> 2     </td><td>  50000</td><td>3      </td><td>1      </td><td>5      </td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 4     </td><td>2      </td><td>2      </td><td>12     </td><td>  20000</td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 4     </td><td>4      </td><td>2      </td><td> 4     </td><td>  35000</td><td>2      </td><td>2      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 4     </td><td>4      </td><td>2      </td><td> 4     </td><td>  30000</td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>3      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 7     </td><td>1      </td><td>2      </td><td> 2     </td><td>  50000</td><td>1      </td><td>1      </td><td>3      </td><td>2      </td><td>5      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 7     </td><td>1      </td><td>2      </td><td> 3     </td><td>  50000</td><td>1      </td><td>2      </td><td>3      </td><td>2      </td><td>5      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>2      </td><td>1      </td><td>4      </td><td> 4     </td><td>2      </td><td>2      </td><td> 7     </td><td>  80000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 1     </td><td>3      </td><td>2      </td><td> 6     </td><td>  20000</td><td>3      </td><td>1      </td><td>3      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td>10     </td><td>2      </td><td>2      </td><td> 2     </td><td>  25000</td><td>3      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>1      </td><td>3      </td><td> 8     </td><td>1      </td><td>1      </td><td> 7     </td><td> 100000</td><td>2      </td><td>1      </td><td>5      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>1      </td><td>2      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 4     </td><td>3      </td><td>2      </td><td> 2     </td><td>  50000</td><td>1      </td><td>2      </td><td>5      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td><td>3      </td><td>3      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 4     </td><td>5      </td><td>2      </td><td> 1     </td><td>  80000</td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>2      </td><td>2      </td><td>6      </td><td> 7     </td><td>5      </td><td>1      </td><td> 2     </td><td> 300000</td><td>3      </td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td><td>3      </td><td>3      </td></tr>
	<tr><td>2      </td><td>2      </td><td>7      </td><td> 7     </td><td>4      </td><td>1      </td><td> 2     </td><td> 200000</td><td>2      </td><td>2      </td><td>4      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td></tr>
	<tr><td>1      </td><td>1      </td><td>2      </td><td> 1     </td><td>2      </td><td>2      </td><td> 5     </td><td>   3000</td><td>1      </td><td>1      </td><td>2      </td><td>1      </td><td>1      </td><td>1      </td><td>1      </td><td>1      </td><td>1      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 2     </td><td>3      </td><td>2      </td><td> 6     </td><td>   4000</td><td>1      </td><td>1      </td><td>1      </td><td>1      </td><td>4      </td><td>2      </td><td>1      </td><td>1      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 4     </td><td>2      </td><td>1      </td><td>10     </td><td> 150000</td><td>3      </td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td></tr>
	<tr><td>2      </td><td>2      </td><td>7      </td><td> 8     </td><td>1      </td><td>2      </td><td> 3     </td><td> 100000</td><td>1      </td><td>2      </td><td>1      </td><td>1      </td><td>5      </td><td>2      </td><td>5      </td><td>4      </td><td>4      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 6     </td><td>1      </td><td>3      </td><td> 2     </td><td>  20000</td><td>3      </td><td>1      </td><td>1      </td><td>1      </td><td>3      </td><td>4      </td><td>3      </td><td>3      </td><td>2      </td></tr>
	<tr><td>2      </td><td>2      </td><td>6      </td><td>10     </td><td>1      </td><td>1      </td><td>10     </td><td>1000000</td><td>3      </td><td>2      </td><td>3      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td></tr>
</tbody>
</table>




```R
library(pequod)
```


```R
attach(a)
```


```R
model1 <-lm(satisf_al~satisf_i+factor(gender))
```


```R
summary(model1)
```



    Call:
    lm(formula = satisf_al ~ satisf_i + factor(gender))

    Residuals:
        Min      1Q  Median      3Q     Max
    -2.7684 -0.2567  0.2137  0.2316  2.2730

    Coefficients:
                    Estimate Std. Error t value Pr(>|t|)
    (Intercept)      1.64979    0.16793   9.824   <2e-16 ***
    satisf_i         0.52966    0.04712  11.240   <2e-16 ***
    factor(gender)2  0.01790    0.07826   0.229    0.819
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.6132 on 244 degrees of freedom
    Multiple R-squared:  0.3418,	Adjusted R-squared:  0.3364
    F-statistic: 63.35 on 2 and 244 DF,  p-value: < 2.2e-16



- Multiple R-squared:  0.3418,	Adjusted R-squared:  0.3364  차이가 0.02 % 정도 차이나면 독립변수로 둘다 잘 선택되었다는 의미이다.

- p-value: < 2.2e-16 이므로 대립가설 만족, 귀무가설 기각됨 , 모델 의미있음

- factor(gender)2  0.01790 (의미)

여자가 남자보다 종속변수가 0.01790 높다. 그러나 Pr값이 0.05보다 높아 유의하지 않다.

> gender 1이 남자고 gender 2가 여자이다.

> 상수: 1.65 , 구매문의만족: 0.53, 성별(여성): 0.02 =>반올림 함

> y(전반만족) =0.53*구매문의만족(x1) + 0.02 * 성별(여성) + 1.65

> 여 : 0.53*구매문의만족 + 1.67

> 남 : 0.53*구매문의만족 + 1.65 (여성만 0.02 곱해줌 ,남자는 0임)


```R
a$gender <-factor(a$gender, levels=c(1,2), labels=c('male','female'))
```


```R
model1 <-lm(a$satisf_al~a$satisf_i+a$gender)
```


```R
summary(model1)
```



    Call:
    lm(formula = a$satisf_al ~ a$satisf_i + a$gender)

    Residuals:
        Min      1Q  Median      3Q     Max
    -2.7684 -0.2567  0.2137  0.2316  2.2730

    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)
    (Intercept)     1.64979    0.16793   9.824   <2e-16 ***
    a$satisf_i      0.52966    0.04712  11.240   <2e-16 ***
    a$genderfemale  0.01790    0.07826   0.229    0.819
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.6132 on 244 degrees of freedom
    Multiple R-squared:  0.3418,	Adjusted R-squared:  0.3364
    F-statistic: 63.35 on 2 and 244 DF,  p-value: < 2.2e-16




```R
model2<-lm(satisf_al~satisf_b + satisf_i + factor(gender) + factor(decision))
```


```R
summary(model2)
```



    Call:
    lm(formula = satisf_al ~ satisf_b + satisf_i + factor(gender) +
        factor(decision))

    Residuals:
        Min      1Q  Median      3Q     Max
    -2.7590 -0.3143  0.1110  0.2992  2.1434

    Coefficients:
                      Estimate Std. Error t value Pr(>|t|)
    (Intercept)        1.67889    0.22582   7.435 1.83e-12 ***
    satisf_b          -0.03889    0.05019  -0.775   0.4392
    satisf_i           0.51617    0.04733  10.906  < 2e-16 ***
    factor(gender)2    0.01963    0.07807   0.251   0.8017
    factor(decision)2  0.05426    0.12075   0.449   0.6536
    factor(decision)3  0.20353    0.10844   1.877   0.0617 .
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.61 on 241 degrees of freedom
    Multiple R-squared:  0.3566,	Adjusted R-squared:  0.3432
    F-statistic: 26.71 on 5 and 241 DF,  p-value: < 2.2e-16



- decision (구매 동기) 1은 사회적 요인 , 2는 심리적인 요인, 3은 외모적 요인이다.

> 결론 : factor(decision)3가 factor(decision)2보다 0.05에 Pr값이 더 가까워서
외모적요인때문에 구매하는 구매자들이 더욱 전반적인 만족도가 높다

> satisf_i(구매문의만족)이 0.516 유의하다는 의미는 다른변수들이 동일한 상태이라는 것이고 구매문의만족도가 대략 1점 차이날때 종속변수가 0.516 정도 차이로 달라진다는 뜻이다.
