---
layout: single
title: "대응표본 t-test분석"
date: "2022-01-17 17:01:00 +0900"
last_modified_at: "2022-01-17 17:34:00 +0900"
---

# 대응표본 t-test분석

##  대응표본이란?

두 집단의 자료를 쌍으로 묶을 수 있을 때

예) 남편과 아내, 쌍둥이, before & after

두 집단의 자료를 쌍으로 묶어야 하기 때문에, 독립표본과는 달리 두 집단의 자료 갯수가 동일해야 함


## 대응표본 조건

##### 참고사이트 - [https://blog.naver.com/istech7/50151133528](https://blog.naver.com/istech7/50151133528)

### 1. 자료는 모두 동일 간격을 가진 연속형 수치여야 한다

키와 몸무게,혈압, 콜레스테롤 수치와 같은 값들은 두 숫자 사이에 끊임없이 다른 숫자가 나타날 수 있는 연속형 수치이며 또한 동일 간격을 가집니다. 이러한 특성을 가진 자료를 비 척도 자료라고 합니다.

반면, IQ 및 온도와 같은 수치도 동일 간격을 가지는 연속형 수치입니다.
하지만 위에서 든 예와는 달리 한 숫자를 다른 숫자에 비해 몇 배 더 크다라고 할 수는 없습니다. 섭씨 40도 온도가 섭씨 20도 온도보다 2배 더 덥다고 할 수 없는 것도 마찬가지입니다. 이러한 특성을 가진 자료를 간격 척도자료라고 합니다.

이렇게 자료가 동일 간격을 가진 연속형 수치인 비 척도 또는 간격 척도인 경우에만 대응 표본 T검정 방법을 사용할 수 있습니다.

### 2. 두 집단은 반드시 서로 짝을 이루고 있어야 합니다.

### 3. 자료의 수치는 정규성을 가져야 합니다. 좌 우가 거의 대칭구조를 보이는 정규분포를 이루어야합니다.




```R
a<- read.csv('cosmetics.csv', header = T)
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



### 연속형 변수 요약통계 한번에 보기

|package|function |statistics |
|------|---|---|
|base|summary()|min, 1Q, median, mean, 3Q, max |
|pastecs| stat.desc()|nbr.val, nbr.null, nbr.na, min, max, range, sum,median, mean, var, std.dev, coef.var,skewness, kurtosis, normtest.W, normtest.p,SE.mean, CI.mean.p|
|psych |describe()|n, mean, std.dev, median, trimmed, mad,min, max, range, skew, kurtosis, se|


```R
library('psych')
```


```R
describe(a$satisf_b)
```


<table>
<thead><tr><th></th><th scope=col>vars</th><th scope=col>n</th><th scope=col>mean</th><th scope=col>sd</th><th scope=col>median</th><th scope=col>trimmed</th><th scope=col>mad</th><th scope=col>min</th><th scope=col>max</th><th scope=col>range</th><th scope=col>skew</th><th scope=col>kurtosis</th><th scope=col>se</th></tr></thead>
<tbody>
	<tr><th scope=row>X1</th><td>1         </td><td>247       </td><td>2.890688  </td><td>0.7809953 </td><td>3         </td><td>2.869347  </td><td>0         </td><td>1         </td><td>5         </td><td>4         </td><td>0.1404754 </td><td>0.3237416 </td><td>0.04969354</td></tr>
</tbody>
</table>




```R
describe(a$satisf_i)
```


<table>
<thead><tr><th></th><th scope=col>vars</th><th scope=col>n</th><th scope=col>mean</th><th scope=col>sd</th><th scope=col>median</th><th scope=col>trimmed</th><th scope=col>mad</th><th scope=col>min</th><th scope=col>max</th><th scope=col>range</th><th scope=col>skew</th><th scope=col>kurtosis</th><th scope=col>se</th></tr></thead>
<tbody>
	<tr><th scope=row>X1</th><td>1         </td><td>247       </td><td>3.404858  </td><td>0.8301096 </td><td>3         </td><td>3.482412  </td><td>1.4826    </td><td>1         </td><td>5         </td><td>4         </td><td>-0.6955943</td><td>0.9204758 </td><td>0.05281861</td></tr>
</tbody>
</table>




```R
t.test(a$satisf_b, a$satisf_i, paired = T)
```



    	Paired t-test

    data:  a$satisf_b and a$satisf_i
    t = -7.1559, df = 246, p-value = 9.519e-12
    alternative hypothesis: true difference in means is not equal to 0
    95 percent confidence interval:
     -0.6556945 -0.3726456
    sample estimates:
    mean of the differences
                   -0.51417




```R
describe(a$satisf_al)
```


<table>
<thead><tr><th></th><th scope=col>vars</th><th scope=col>n</th><th scope=col>mean</th><th scope=col>sd</th><th scope=col>median</th><th scope=col>trimmed</th><th scope=col>mad</th><th scope=col>min</th><th scope=col>max</th><th scope=col>range</th><th scope=col>skew</th><th scope=col>kurtosis</th><th scope=col>se</th></tr></thead>
<tbody>
	<tr><th scope=row>X1</th><td>1         </td><td>247       </td><td>3.461538  </td><td>0.7527311 </td><td>4         </td><td>3.512563  </td><td>1.4826    </td><td>1         </td><td>5         </td><td>4         </td><td>-0.9803738</td><td>2.161749  </td><td>0.04789513</td></tr>
</tbody>
</table>




```R
describe(a$repurchase)
```


<table>
<thead><tr><th></th><th scope=col>vars</th><th scope=col>n</th><th scope=col>mean</th><th scope=col>sd</th><th scope=col>median</th><th scope=col>trimmed</th><th scope=col>mad</th><th scope=col>min</th><th scope=col>max</th><th scope=col>range</th><th scope=col>skew</th><th scope=col>kurtosis</th><th scope=col>se</th></tr></thead>
<tbody>
	<tr><th scope=row>X1</th><td>1        </td><td>247      </td><td>3.554656 </td><td>0.724182 </td><td>4        </td><td>3.633166 </td><td>0        </td><td>1        </td><td>5        </td><td>4        </td><td>-1.27728 </td><td>2.554179 </td><td>0.0460786</td></tr>
</tbody>
</table>




```R
t.test(a$satisf_al, a$repurchase, paired=T)
# p-value < 0.05(기준) 귀무가설은 기각된다.
```



    	Paired t-test

    data:  a$satisf_al and a$repurchase
    t = -2.1233, df = 246, p-value = 0.03472
    alternative hypothesis: true difference in means is not equal to 0
    95 percent confidence interval:
     -0.179495526 -0.006739292
    sample estimates:
    mean of the differences
                -0.09311741




```R
t.test(a$repurchase, a$satisf_al, paired=T)
```



    	Paired t-test

    data:  a$repurchase and a$satisf_al
    t = 2.1233, df = 246, p-value = 0.03472
    alternative hypothesis: true difference in means is not equal to 0
    95 percent confidence interval:
     0.006739292 0.179495526
    sample estimates:
    mean of the differences
                 0.09311741



##### 결론 - 통계적으로 유의하다 대립가설 성립

# 밀도함수 그리기


```R
library(ggplot2)
```


    Attaching package: 'ggplot2'

    The following objects are masked from 'package:psych':

        %+%, alpha




```R
ggplot(data=a, aes(x=satisf_b, fill= 'red'))+geom_density(adjust=1.5)
```



![output_16_0]({{ site.gdrive_url_prefix }}1WYsczVBkIICHGvxvBzU4ydG1QmL6Mvus)


