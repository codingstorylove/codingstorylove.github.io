---
layout: single
title: "상관관계분석의 개념과 원리"
date: "2022-01-19 13:34:00 +0900"
last_modified_at: "2022-01-19 13:34:10 +0900"
---


# 상관관계분석의 개념과 원리

## 개념

- 두 연속형 변수 간의 상관 정도를 파악

- 상관계수를 통해 표준화된 상관관계를 도출

- 주로 회귀분석을 하기 전에 독립변수 , 종속변수 간의 상관이 있는지 탐색



```R
a <- read.csv('cosmetics.csv',header = T)
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
install.packages('corrplot')
library(corrplot)
```


      There is a binary version available but the source version is later:
             binary source needs_compilation
    corrplot   0.88   0.92             FALSE



    installing the source package 'corrplot'

    corrplot 0.92 loaded


## Correlation, Variance and Covariance (Matrices)

Description

var, cov and cor compute the variance of x and the covariance or correlation of x and y if these are vectors. If x and y are matrices then the covariances (or correlations) between the columns of x and the columns of y are computed.
cov2cor scales a covariance matrix into the corresponding correlation matrix efficiently.

- Usage

var(x, y = NULL, na.rm = FALSE, use)

cov(x, y = NULL, use = "everything", method = c("pearson", "kendall", "spearman"))

cor(x, y = NULL, use = "everything", method = c("pearson", "kendall", "spearman"))

cov2cor(V)

- Arguments

x : a numeric vector, matrix or data frame.

y : NULL (default) or a vector, matrix or data frame with compatible dimensions to x.
The default is equivalent to y = x (but more efficient).

na.rm : logical. Should missing values be removed?

use : an optional character string giving a method for computing covariances in the presence of missing values.
This must be (an abbreviation of) one of the strings "everything", "all.obs", "complete.obs", "na.or.complete", or "pairwise.complete.obs".

method : a character string indicating which correlation coefficient (or covariance) is to be computed.
One of "pearson" (default), "kendall", or "spearman": can be abbreviated.

V : symmetric numeric matrix, usually positive definite such as a covariance matrix.


```R
cor(a, method='pearson')
```


<table>
<thead><tr><th></th><th scope=col>gender</th><th scope=col>marriage</th><th scope=col>edu</th><th scope=col>job</th><th scope=col>mincome</th><th scope=col>aware</th><th scope=col>count</th><th scope=col>amount</th><th scope=col>decision</th><th scope=col>propensity</th><th scope=col>skin</th><th scope=col>promo</th><th scope=col>location</th><th scope=col>satisf_b</th><th scope=col>satisf_i</th><th scope=col>satisf_al</th><th scope=col>repurchase</th></tr></thead>
<tbody>
	<tr><th scope=row>gender</th><td> 1.00000000 </td><td> 0.018950432</td><td>-0.015141892</td><td> 0.234495300</td><td>-0.24322115 </td><td>-0.173258517</td><td> 0.26720736 </td><td> 0.07060766 </td><td> 0.024599925</td><td> 0.105123747</td><td> 0.100927559</td><td> 0.031070486</td><td>-0.07987533 </td><td> 0.068425536</td><td> 0.03371565 </td><td> 0.031582421</td><td> 0.13717521 </td></tr>
	<tr><th scope=row>marriage</th><td> 0.01895043 </td><td> 1.000000000</td><td> 0.090430642</td><td>-0.097376313</td><td> 0.34672053 </td><td> 0.002746706</td><td>-0.03388587 </td><td> 0.11179503 </td><td> 0.065853663</td><td> 0.161741172</td><td> 0.000243840</td><td> 0.056206150</td><td>-0.06633169 </td><td> 0.037174065</td><td> 0.08363306 </td><td> 0.104428021</td><td> 0.16562358 </td></tr>
	<tr><th scope=row>edu</th><td>-0.01514189 </td><td> 0.090430642</td><td> 1.000000000</td><td>-0.152514543</td><td> 0.29125234 </td><td>-0.053224463</td><td> 0.02317484 </td><td> 0.10287980 </td><td> 0.008078891</td><td> 0.144735867</td><td>-0.048833264</td><td> 0.016601180</td><td>-0.16879183 </td><td>-0.017346951</td><td> 0.09259939 </td><td> 0.020172944</td><td> 0.03726943 </td></tr>
	<tr><th scope=row>job</th><td> 0.23449530 </td><td>-0.097376313</td><td>-0.152514543</td><td> 1.000000000</td><td>-0.29724975 </td><td>-0.037035494</td><td> 0.06774745 </td><td>-0.04452342 </td><td> 0.015581175</td><td>-0.148122023</td><td> 0.036206215</td><td> 0.033042235</td><td> 0.21765244 </td><td>-0.007970738</td><td> 0.07370249 </td><td>-0.054013630</td><td> 0.04257733 </td></tr>
	<tr><th scope=row>mincome</th><td>-0.24322115 </td><td> 0.346720533</td><td> 0.291252342</td><td>-0.297249748</td><td> 1.00000000 </td><td> 0.033181017</td><td>-0.03751775 </td><td> 0.12555069 </td><td> 0.093481119</td><td> 0.291048862</td><td> 0.002721420</td><td> 0.041308095</td><td>-0.27159896 </td><td> 0.041790891</td><td> 0.11493493 </td><td> 0.121591093</td><td> 0.11494068 </td></tr>
	<tr><th scope=row>aware</th><td>-0.17325852 </td><td> 0.002746706</td><td>-0.053224463</td><td>-0.037035494</td><td> 0.03318102 </td><td> 1.000000000</td><td>-0.14045380 </td><td> 0.02599560 </td><td> 0.083566385</td><td> 0.002056555</td><td>-0.057377153</td><td> 0.004190501</td><td>-0.01139770 </td><td> 0.097678118</td><td>-0.02809058 </td><td> 0.016987040</td><td>-0.09646385 </td></tr>
	<tr><th scope=row>count</th><td> 0.26720736 </td><td>-0.033885869</td><td> 0.023174842</td><td> 0.067747449</td><td>-0.03751775 </td><td>-0.140453801</td><td> 1.00000000 </td><td>-0.06605694 </td><td>-0.034378011</td><td> 0.010766170</td><td> 0.039127374</td><td> 0.010843867</td><td> 0.01737366 </td><td>-0.023712383</td><td> 0.17298313 </td><td> 0.121654091</td><td> 0.19176923 </td></tr>
	<tr><th scope=row>amount</th><td> 0.07060766 </td><td> 0.111795032</td><td> 0.102879797</td><td>-0.044523425</td><td> 0.12555069 </td><td> 0.025995598</td><td>-0.06605694 </td><td> 1.00000000 </td><td>-0.092237915</td><td> 0.248702226</td><td> 0.039647452</td><td> 0.167832282</td><td>-0.21865952 </td><td> 0.151351288</td><td> 0.05486640 </td><td> 0.063516185</td><td> 0.05797403 </td></tr>
	<tr><th scope=row>decision</th><td> 0.02459993 </td><td> 0.065853663</td><td> 0.008078891</td><td> 0.015581175</td><td> 0.09348112 </td><td> 0.083566385</td><td>-0.03437801 </td><td>-0.09223791 </td><td> 1.000000000</td><td> 0.104598639</td><td> 0.103755420</td><td> 0.022390779</td><td>-0.10788193 </td><td> 0.003375614</td><td> 0.13588654 </td><td> 0.189271220</td><td> 0.21929154 </td></tr>
	<tr><th scope=row>propensity</th><td> 0.10512375 </td><td> 0.161741172</td><td> 0.144735867</td><td>-0.148122023</td><td> 0.29104886 </td><td> 0.002056555</td><td> 0.01076617 </td><td> 0.24870223 </td><td> 0.104598639</td><td> 1.000000000</td><td>-0.098094475</td><td> 0.197142362</td><td>-0.27947384 </td><td> 0.323968388</td><td> 0.21183616 </td><td> 0.180745009</td><td> 0.22548460 </td></tr>
	<tr><th scope=row>skin</th><td> 0.10092756 </td><td> 0.000243840</td><td>-0.048833264</td><td> 0.036206215</td><td> 0.00272142 </td><td>-0.057377153</td><td> 0.03912737 </td><td> 0.03964745 </td><td> 0.103755420</td><td>-0.098094475</td><td> 1.000000000</td><td> 0.003177493</td><td> 0.02155061 </td><td>-0.127471531</td><td> 0.06872337 </td><td> 0.011722962</td><td> 0.02536274 </td></tr>
	<tr><th scope=row>promo</th><td> 0.03107049 </td><td> 0.056206150</td><td> 0.016601180</td><td> 0.033042235</td><td> 0.04130810 </td><td> 0.004190501</td><td> 0.01084387 </td><td> 0.16783228 </td><td> 0.022390779</td><td> 0.197142362</td><td> 0.003177493</td><td> 1.000000000</td><td>-0.03164168 </td><td> 0.072483016</td><td> 0.13940641 </td><td>-0.005563814</td><td> 0.10102533 </td></tr>
	<tr><th scope=row>location</th><td>-0.07987533 </td><td>-0.066331688</td><td>-0.168791830</td><td> 0.217652440</td><td>-0.27159896 </td><td>-0.011397701</td><td> 0.01737366 </td><td>-0.21865952 </td><td>-0.107881932</td><td>-0.279473839</td><td> 0.021550612</td><td>-0.031641677</td><td> 1.00000000 </td><td>-0.254221863</td><td>-0.08922635 </td><td>-0.095974739</td><td> 0.05341473 </td></tr>
	<tr><th scope=row>satisf_b</th><td> 0.06842554 </td><td> 0.037174065</td><td>-0.017346951</td><td>-0.007970738</td><td> 0.04179089 </td><td> 0.097678118</td><td>-0.02371238 </td><td> 0.15135129 </td><td> 0.003375614</td><td> 0.323968388</td><td>-0.127471531</td><td> 0.072483016</td><td>-0.25422186 </td><td> 1.000000000</td><td> 0.01837903 </td><td>-0.031382338</td><td>-0.02892399 </td></tr>
	<tr><th scope=row>satisf_i</th><td> 0.03371565 </td><td> 0.083633059</td><td> 0.092599391</td><td> 0.073702488</td><td> 0.11493493 </td><td>-0.028090585</td><td> 0.17298313 </td><td> 0.05486640 </td><td> 0.135886536</td><td> 0.211836156</td><td> 0.068723366</td><td> 0.139406409</td><td>-0.08922635 </td><td> 0.018379033</td><td> 1.00000000 </td><td> 0.584506125</td><td> 0.51077138 </td></tr>
	<tr><th scope=row>satisf_al</th><td> 0.03158242 </td><td> 0.104428021</td><td> 0.020172944</td><td>-0.054013630</td><td> 0.12159109 </td><td> 0.016987040</td><td> 0.12165409 </td><td> 0.06351618 </td><td> 0.189271220</td><td> 0.180745009</td><td> 0.011722962</td><td>-0.005563814</td><td>-0.09597474 </td><td>-0.031382338</td><td> 0.58450612 </td><td> 1.000000000</td><td> 0.56502825 </td></tr>
	<tr><th scope=row>repurchase</th><td> 0.13717521 </td><td> 0.165623585</td><td> 0.037269434</td><td> 0.042577334</td><td> 0.11494068 </td><td>-0.096463853</td><td> 0.19176923 </td><td> 0.05797403 </td><td> 0.219291539</td><td> 0.225484605</td><td> 0.025362744</td><td> 0.101025333</td><td> 0.05341473 </td><td>-0.028923989</td><td> 0.51077138 </td><td> 0.565028245</td><td> 1.00000000 </td></tr>
</tbody>
</table>




```R
attach(a)
```

    The following objects are masked from a (pos = 3):

        amount, aware, count, decision, edu, gender, job, location,
        marriage, mincome, promo, propensity, repurchase, satisf_al,
        satisf_b, satisf_i, skin




```R
library(base)
```


```R
cor <- cbind(decision,satisf_b, satisf_i, satisf_al, repurchase)
```


```R
cor
```


<table>
<thead><tr><th scope=col>decision</th><th scope=col>satisf_b</th><th scope=col>satisf_i</th><th scope=col>satisf_al</th><th scope=col>repurchase</th></tr></thead>
<tbody>
	<tr><td>2</td><td>5</td><td>2</td><td>2</td><td>2</td></tr>
	<tr><td>1</td><td>2</td><td>3</td><td>3</td><td>4</td></tr>
	<tr><td>3</td><td>4</td><td>5</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>2</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>2</td><td>3</td><td>4</td><td>4</td><td>3</td></tr>
	<tr><td>3</td><td>2</td><td>2</td><td>3</td><td>4</td></tr>
	<tr><td>3</td><td>4</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>2</td><td>3</td><td>4</td><td>4</td></tr>
	<tr><td>1</td><td>3</td><td>3</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>2</td><td>4</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>1</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>3</td><td>3</td><td>3</td><td>4</td></tr>
	<tr><td>1</td><td>3</td><td>4</td><td>3</td><td>3</td></tr>
	<tr><td>1</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>4</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>2</td><td>3</td><td>3</td><td>4</td><td>4</td></tr>
	<tr><td>2</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>2</td><td>3</td><td>4</td><td>4</td></tr>
	<tr><td>2</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>1</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>1</td><td>3</td><td>3</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>2</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>2</td><td>4</td><td>4</td><td>4</td><td>3</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>2</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>1</td><td>5</td><td>1</td><td>3</td><td>1</td></tr>
	<tr><td>1</td><td>2</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>2</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>1</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>4</td><td>4</td><td>3</td><td>4</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>2</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>3</td><td>2</td><td>3</td><td>4</td></tr>
	<tr><td>2</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>2</td><td>3</td><td>3</td><td>4</td><td>4</td></tr>
	<tr><td>2</td><td>3</td><td>4</td><td>4</td><td>3</td></tr>
	<tr><td>1</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>1</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>2</td><td>3</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>2</td><td>3</td><td>3</td><td>1</td><td>2</td></tr>
	<tr><td>1</td><td>3</td><td>4</td><td>3</td><td>3</td></tr>
	<tr><td>2</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>3</td><td>3</td></tr>
	<tr><td>2</td><td>3</td><td>3</td><td>3</td><td>3</td></tr>
	<tr><td>1</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
	<tr><td>1</td><td>2</td><td>1</td><td>1</td><td>1</td></tr>
	<tr><td>3</td><td>3</td><td>4</td><td>4</td><td>4</td></tr>
	<tr><td>1</td><td>2</td><td>5</td><td>4</td><td>4</td></tr>
	<tr><td>3</td><td>4</td><td>3</td><td>3</td><td>2</td></tr>
	<tr><td>3</td><td>2</td><td>3</td><td>3</td><td>3</td></tr>
</tbody>
</table>




```R
cor(cor, method='pearson')
```


<table>
<thead><tr><th></th><th scope=col>decision</th><th scope=col>satisf_b</th><th scope=col>satisf_i</th><th scope=col>satisf_al</th><th scope=col>repurchase</th></tr></thead>
<tbody>
	<tr><th scope=row>decision</th><td>1.000000000 </td><td> 0.003375614</td><td>0.13588654  </td><td> 0.18927122 </td><td> 0.21929154 </td></tr>
	<tr><th scope=row>satisf_b</th><td>0.003375614 </td><td> 1.000000000</td><td>0.01837903  </td><td>-0.03138234 </td><td>-0.02892399 </td></tr>
	<tr><th scope=row>satisf_i</th><td>0.135886536 </td><td> 0.018379033</td><td>1.00000000  </td><td> 0.58450612 </td><td> 0.51077138 </td></tr>
	<tr><th scope=row>satisf_al</th><td>0.189271220 </td><td>-0.031382338</td><td>0.58450612  </td><td> 1.00000000 </td><td> 0.56502825 </td></tr>
	<tr><th scope=row>repurchase</th><td>0.219291539 </td><td>-0.028923989</td><td>0.51077138  </td><td> 0.56502825 </td><td> 1.00000000 </td></tr>
</tbody>
</table>




```R
pairs(cor)
```



![output_11_0]({{ site.gdrive_url_prefix }}1O69MCNlf0oew_ZoS-zfDAclN5WiZedss)




```R
library(ggplot2)
```


```R
ggplot(as.data.frame(cor),aes(x=satisf_al, y= repurchase)) + geom_point()
```



![output_13_0]({{ site.gdrive_url_prefix }}188yjDqae7IWVsDZStGaM3_3ODDk1aMCN)




```R
ggplot(as.data.frame(cor),aes(x=satisf_al, y= repurchase, shape=factor(decision))) + geom_point(color= 'red',fill='blue', alpha=0.5, size=6,stroke=2)
```


![output_14_0]({{ site.gdrive_url_prefix }}1Hn_8naAvsaL3O3NLPEWK03msfGt3Zw3z)




```R
ggplot(as.data.frame(cor),aes(x=satisf_al, y= repurchase)) + geom_smooth(method=lm)
```

    `geom_smooth()` using formula 'y ~ x'




![output_15_1]({{ site.gdrive_url_prefix }}1VBTetdtRrpCBByiVkqcb-xyxo4PmzqR3)



- 회색부분이 신뢰구간을 나타내 준다,
