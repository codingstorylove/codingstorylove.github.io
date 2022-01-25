---
layout: single
title: "로지스틱 회귀분석의 개념과 원리"
date: "2022-01-21 23:18:00 +0900"
last_modified_at: "2022-01-21 23:18:10 +0900"
---

# 로지스틱 회귀분석의 개념과 원리




- 어떤 사건이 발생할지에 대한 직접 예측이 아니라 그 사건이 발생할 확률을 예측하는 것이다.

> 로지스틱 회귀분석이 사용되는 예를 보면,

- 금융권에서는 고객의 신용도 평가를 통해 이 고객의 신용도가 우량이 될 것인지, 신용불량자가 될 것인지 미리 예측해 볼 수 있다. 또한 통신사의 경우 2년 약정 종료 후 번호이동으로 타 통신사로 갈 것인지, 기기변경으로 남을 것인지 판단할 수 있다.

- 제과점에서는 전날 저녁에 다음날 팔게 될 빵의 수량을 예측해서 본사에 주문하거나 직접 만들게 되는데 이때 재고를 남기지 않고 모두 판매할 수 있을지 폐기하게 될지 등을 예측하는데 활용해 볼 수 있을 것이다.

- 무엇보다 의학 분야에서는 다양한 원인을 파악하여 질병에 대한 예측을 하는데 더욱 효과적으로 활용해 볼 수 있을 것이다.

- 종속변수 : 이분형(0 또는 1의 값을 가짐)

- 독립변수 : 범주형 OR 연속형

- 종속변수 값, 즉 확률이 0.5보다 크면 그 사건이 일어나며, 0.5보다 작으며 그 사건이 일어나지 않는 것으로 예측

![log1]({{ site.gdrive_url_prefix }}1cVB-6XrqRCrEN0ljijI1g1xFg8mzwCo3)


- 참고사이트 : [https://m.blog.naver.com/y4769/221851780608](https://m.blog.naver.com/y4769/221851780608)

- 보건과학통계 SPSS 이야기 / 네이버 블로그 참고함

## 오즈(odds) 이란?

- 일반적 비율(활률) = p/1 = 발생할확률/전체

- 오즈 = p/(1-p) = 발생활확률/발생하지않을확률



|소득구간|명수|소유자|미소유자|소유비율|오즈|
|--------|---|---|---|---|---|
|100만원 미만|100|10|90|10/100|10/90|
|200만원 미만|100|18|82|18/100|18/82|


> 확률이 아닌 오즈비로 계산하는 이유

- 다른집단과의 비교값인 오즈비가 현실세계에서 더욱 타당한 의미를 가짐


## 로짓으로의 변환

- 오즈에 자연로그(log)를 취하여 자료를 반환함

- 로그변환시 자료가 선형적으로 안정화되며 음수와 양수 전체 구간에 무한대로 존재하게 됨

# 실습과 시각화


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
attach(a)
```


```R
library(car)
```

    Loading required package: carData



```R
a$repurchase_re <- recode(a$repurchase, "lo:3=0; 4:hi=1")
```


```R
a
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>marriage</th><th scope=col>edu</th><th scope=col>job</th><th scope=col>mincome</th><th scope=col>aware</th><th scope=col>count</th><th scope=col>amount</th><th scope=col>decision</th><th scope=col>propensity</th><th scope=col>skin</th><th scope=col>promo</th><th scope=col>location</th><th scope=col>satisf_b</th><th scope=col>satisf_i</th><th scope=col>satisf_al</th><th scope=col>repurchase</th><th scope=col>repurchase_re</th></tr></thead>
<tbody>
	<tr><td>1      </td><td>1      </td><td>4      </td><td>1      </td><td>2      </td><td> 2     </td><td> 1     </td><td>  11000</td><td>2      </td><td>1      </td><td>1      </td><td>1      </td><td>2      </td><td>5      </td><td>2      </td><td>2      </td><td>2      </td><td>0      </td></tr>
	<tr><td>2      </td><td>1      </td><td>4      </td><td>9      </td><td>2      </td><td> 1     </td><td> 4     </td><td>  30000</td><td>1      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>4      </td><td>3      </td><td> 1     </td><td> 6     </td><td> 100000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>4      </td><td>5      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>7      </td><td>5      </td><td> 2     </td><td> 6     </td><td>  65000</td><td>3      </td><td>2      </td><td>5      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>2      </td><td>6      </td><td>6      </td><td>5      </td><td> 2     </td><td> 2     </td><td>  50000</td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>2      </td><td>7      </td><td>3      </td><td> 1     </td><td> 2     </td><td> 100000</td><td>2      </td><td>1      </td><td>4      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>1      </td><td>6      </td><td>4      </td><td>5      </td><td> 1     </td><td> 5     </td><td> 100000</td><td>3      </td><td>2      </td><td>5      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>1      </td><td>6      </td><td>4      </td><td>5      </td><td> 4     </td><td>10     </td><td>  39000</td><td>3      </td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>4      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>5      </td><td>2      </td><td> 2     </td><td> 2     </td><td>  40000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>5      </td><td>2      </td><td> 1     </td><td> 2     </td><td> 100000</td><td>3      </td><td>3      </td><td>3      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>1      </td><td>7      </td><td>4      </td><td>3      </td><td>10     </td><td> 3     </td><td>  50000</td><td>1      </td><td>3      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>1      </td><td>2      </td><td>5      </td><td>3      </td><td> 2     </td><td> 1     </td><td>  30000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>4      </td><td>3      </td><td> 4     </td><td> 4     </td><td> 320000</td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>2      </td><td>4      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>4      </td><td>2      </td><td> 3     </td><td> 2     </td><td> 200000</td><td>1      </td><td>2      </td><td>3      </td><td>1      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td>4      </td><td>6      </td><td> 2     </td><td> 2     </td><td>  60000</td><td>3      </td><td>2      </td><td>1      </td><td>2      </td><td>5      </td><td>3      </td><td>3      </td><td>3      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>1      </td><td>4      </td><td>5      </td><td>2      </td><td> 2     </td><td> 3     </td><td>  50000</td><td>1      </td><td>2      </td><td>4      </td><td>1      </td><td>3      </td><td>3      </td><td>4      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>2      </td><td>8      </td><td>3      </td><td>2      </td><td> 5     </td><td> 3     </td><td>1000000</td><td>1      </td><td>3      </td><td>1      </td><td>2      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>1      </td><td>3      </td><td>8      </td><td>5      </td><td> 1     </td><td> 6     </td><td>1500000</td><td>3      </td><td>3      </td><td>2      </td><td>4      </td><td>1      </td><td>4      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>2      </td><td>2      </td><td>6      </td><td>2      </td><td> 4     </td><td> 1     </td><td>  80000</td><td>2      </td><td>3      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td>4      </td><td>3      </td><td> 8     </td><td> 3     </td><td>  30000</td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>2      </td><td>4      </td><td>2      </td><td> 8     </td><td> 4     </td><td> 350000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>7      </td><td>6      </td><td> 1     </td><td> 4     </td><td> 250000</td><td>3      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>7      </td><td>3      </td><td> 1     </td><td>25     </td><td>  50000</td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>2      </td><td>9      </td><td>1      </td><td> 1     </td><td> 1     </td><td>  20000</td><td>1      </td><td>1      </td><td>5      </td><td>1      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>1      </td><td>3      </td><td>8      </td><td>4      </td><td> 2     </td><td> 3     </td><td>  42000</td><td>1      </td><td>2      </td><td>3      </td><td>1      </td><td>3      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td>8      </td><td>4      </td><td> 2     </td><td> 3     </td><td>  42000</td><td>3      </td><td>3      </td><td>2      </td><td>1      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>1      </td><td>4      </td><td>4      </td><td>3      </td><td> 2     </td><td>20     </td><td>  40000</td><td>3      </td><td>1      </td><td>5      </td><td>2      </td><td>3      </td><td>2      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td>4      </td><td>6      </td><td> 1     </td><td> 6     </td><td>  70000</td><td>3      </td><td>3      </td><td>5      </td><td>2      </td><td>1      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>8      </td><td>4      </td><td>5      </td><td> 5     </td><td> 6     </td><td> 200000</td><td>2      </td><td>1      </td><td>4      </td><td>1      </td><td>1      </td><td>4      </td><td>4      </td><td>4      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td>2      </td><td>6      </td><td> 2     </td><td> 1     </td><td> 200000</td><td>3      </td><td>2      </td><td>1      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 5     </td><td>2      </td><td>1      </td><td>10     </td><td>  30000</td><td>2      </td><td>2      </td><td>2      </td><td>4      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 6     </td><td>6      </td><td>7      </td><td> 5     </td><td>  50000</td><td>3      </td><td>2      </td><td>4      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 1     </td><td>4      </td><td>1      </td><td> 1     </td><td>  10000</td><td>1      </td><td>3      </td><td>1      </td><td>1      </td><td>1      </td><td>5      </td><td>1      </td><td>3      </td><td>1      </td><td>0      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 4     </td><td>3      </td><td>2      </td><td> 1     </td><td>  10000</td><td>1      </td><td>1      </td><td>4      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 4     </td><td>3      </td><td>2      </td><td> 3     </td><td>  50000</td><td>3      </td><td>2      </td><td>5      </td><td>2      </td><td>3      </td><td>2      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>2      </td><td>2      </td><td> 5     </td><td>4      </td><td>2      </td><td> 1     </td><td>  60000</td><td>1      </td><td>1      </td><td>1      </td><td>4      </td><td>5      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>1      </td><td>6      </td><td> 1     </td><td>3      </td><td>2      </td><td> 2     </td><td>  50000</td><td>3      </td><td>2      </td><td>5      </td><td>1      </td><td>2      </td><td>4      </td><td>4      </td><td>3      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>6      </td><td> 4     </td><td>6      </td><td>1      </td><td> 3     </td><td> 500000</td><td>3      </td><td>3      </td><td>5      </td><td>2      </td><td>1      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 1     </td><td>3      </td><td>2      </td><td> 1     </td><td>  50000</td><td>3      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>6      </td><td> 3     </td><td>4      </td><td>1      </td><td> 1     </td><td> 100000</td><td>3      </td><td>2      </td><td>1      </td><td>2      </td><td>1      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 7     </td><td>4      </td><td>1      </td><td> 2     </td><td>  50000</td><td>3      </td><td>1      </td><td>5      </td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 4     </td><td>2      </td><td>2      </td><td>12     </td><td>  20000</td><td>2      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 4     </td><td>4      </td><td>2      </td><td> 4     </td><td>  35000</td><td>2      </td><td>2      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 4     </td><td>4      </td><td>2      </td><td> 4     </td><td>  30000</td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 7     </td><td>1      </td><td>2      </td><td> 2     </td><td>  50000</td><td>1      </td><td>1      </td><td>3      </td><td>2      </td><td>5      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 7     </td><td>1      </td><td>2      </td><td> 3     </td><td>  50000</td><td>1      </td><td>2      </td><td>3      </td><td>2      </td><td>5      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>1      </td><td>4      </td><td> 4     </td><td>2      </td><td>2      </td><td> 7     </td><td>  80000</td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 1     </td><td>3      </td><td>2      </td><td> 6     </td><td>  20000</td><td>3      </td><td>1      </td><td>3      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td>10     </td><td>2      </td><td>2      </td><td> 2     </td><td>  25000</td><td>3      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>1      </td><td>3      </td><td> 8     </td><td>1      </td><td>1      </td><td> 7     </td><td> 100000</td><td>2      </td><td>1      </td><td>5      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>1      </td><td>2      </td><td>0      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 4     </td><td>3      </td><td>2      </td><td> 2     </td><td>  50000</td><td>1      </td><td>2      </td><td>5      </td><td>2      </td><td>2      </td><td>3      </td><td>4      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>2      </td><td>4      </td><td> 4     </td><td>5      </td><td>2      </td><td> 1     </td><td>  80000</td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>6      </td><td> 7     </td><td>5      </td><td>1      </td><td> 2     </td><td> 300000</td><td>3      </td><td>3      </td><td>2      </td><td>3      </td><td>2      </td><td>3      </td><td>4      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>7      </td><td> 7     </td><td>4      </td><td>1      </td><td> 2     </td><td> 200000</td><td>2      </td><td>2      </td><td>4      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
	<tr><td>1      </td><td>1      </td><td>2      </td><td> 1     </td><td>2      </td><td>2      </td><td> 5     </td><td>   3000</td><td>1      </td><td>1      </td><td>2      </td><td>1      </td><td>1      </td><td>1      </td><td>1      </td><td>1      </td><td>1      </td><td>0      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 2     </td><td>3      </td><td>2      </td><td> 6     </td><td>   4000</td><td>1      </td><td>1      </td><td>1      </td><td>1      </td><td>4      </td><td>2      </td><td>1      </td><td>1      </td><td>1      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>4      </td><td> 4     </td><td>2      </td><td>1      </td><td>10     </td><td> 150000</td><td>3      </td><td>2      </td><td>2      </td><td>1      </td><td>2      </td><td>3      </td><td>4      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>2      </td><td>2      </td><td>7      </td><td> 8     </td><td>1      </td><td>2      </td><td> 3     </td><td> 100000</td><td>1      </td><td>2      </td><td>1      </td><td>1      </td><td>5      </td><td>2      </td><td>5      </td><td>4      </td><td>4      </td><td>1      </td></tr>
	<tr><td>1      </td><td>1      </td><td>4      </td><td> 6     </td><td>1      </td><td>3      </td><td> 2     </td><td>  20000</td><td>3      </td><td>1      </td><td>1      </td><td>1      </td><td>3      </td><td>4      </td><td>3      </td><td>3      </td><td>2      </td><td>0      </td></tr>
	<tr><td>2      </td><td>2      </td><td>6      </td><td>10     </td><td>1      </td><td>1      </td><td>10     </td><td>1000000</td><td>3      </td><td>2      </td><td>3      </td><td>1      </td><td>3      </td><td>2      </td><td>3      </td><td>3      </td><td>3      </td><td>0      </td></tr>
</tbody>
</table>




```R
install.packages("ROCR")
```

    package 'ROCR' successfully unpacked and MD5 sums checked

    The downloaded binary packages are in
    	C:\Users\MyCom\AppData\Local\Temp\RtmpM1usDv\downloaded_packages



```R
install.packages("aod")
```

    package 'aod' successfully unpacked and MD5 sums checked

    The downloaded binary packages are in
    	C:\Users\MyCom\AppData\Local\Temp\RtmpM1usDv\downloaded_packages



```R
library(ROCR)
```

    Warning message:
    "package 'ROCR' was built under R version 3.6.3"


```R
library(aod)
```

    Warning message:
    "package 'aod' was built under R version 3.6.3"


```R
logit.model<-glm(repurchase_re ~ factor(propensity)+ factor(decision)+satisf_al, family = binomial, data=a)
```


```R
summary(logit.model)
```



    Call:
    glm(formula = repurchase_re ~ factor(propensity) + factor(decision) +
        satisf_al, family = binomial, data = a)

    Deviance Residuals:
        Min       1Q   Median       3Q      Max
    -2.7048  -0.8706   0.3504   0.6925   3.1756

    Coefficients:
                        Estimate Std. Error z value Pr(>|z|)
    (Intercept)         -7.15812    1.13359  -6.315 2.71e-10 ***
    factor(propensity)2  0.04172    0.38248   0.109   0.9131
    factor(propensity)3  1.20851    0.52581   2.298   0.0215 *
    factor(decision)2    0.09932    0.49970   0.199   0.8425
    factor(decision)3    0.38632    0.44657   0.865   0.3870
    satisf_al            2.08071    0.30725   6.772 1.27e-11 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    (Dispersion parameter for binomial family taken to be 1)

        Null deviance: 330.95  on 246  degrees of freedom
    Residual deviance: 241.91  on 241  degrees of freedom
    AIC: 253.91

    Number of Fisher Scoring iterations: 5



> satisf_al(전반만족도)가 높을수록 재구매 할 확률이 높다


![log2]({{ site.gdrive_url_prefix }}1nHDZdIcLDk1BUAAJAGednPeOmYk_0pvo)

> factor(propensity)3 ,구매성향이 비교적 고가의 제품을 선호하는 구매자들이 재구매 할 확률이 높다. (저가의 제품을 선호하는 구매자들보다)


```R
exp(cbind(OR=coef(logit.model),confint(logit.model))) #신뢰구간 : confint
# OR : 오즈비로 변경해줌
```

    Waiting for profiling to be done...



<table>
<thead><tr><th></th><th scope=col>OR</th><th scope=col>2.5 %</th><th scope=col>97.5 %</th></tr></thead>
<tbody>
	<tr><th scope=row>(Intercept)</th><td>0.0007785181</td><td>7.572483e-05</td><td> 0.006469474</td></tr>
	<tr><th scope=row>factor(propensity)2</th><td>1.0426040653</td><td>4.898358e-01</td><td> 2.207607940</td></tr>
	<tr><th scope=row>factor(propensity)3</th><td>3.3484842168</td><td>1.224880e+00</td><td> 9.775048167</td></tr>
	<tr><th scope=row>factor(decision)2</th><td>1.1044182635</td><td>4.125024e-01</td><td> 2.952278838</td></tr>
	<tr><th scope=row>factor(decision)3</th><td>1.4715484016</td><td>6.096242e-01</td><td> 3.542608278</td></tr>
	<tr><th scope=row>satisf_al</th><td>8.0101445633</td><td>4.512294e+00</td><td>15.057682143</td></tr>
</tbody>
</table>



> 결론 : satisf_al, 전반만족도가 1 정도 증가할때 재구매 확률이 8.0101445633배 증가한다.

> factor(propensity)3 , 저가의 제품을 선호하는 구매자에 비해 고가의 제품을 선호하는 구매자가 3배 더 많이 구매할 의향이 있다,


```R
install.packages('pscl')
```

    package 'pscl' successfully unpacked and MD5 sums checked

    The downloaded binary packages are in
    	C:\Users\MyCom\AppData\Local\Temp\RtmpM1usDv\downloaded_packages



```R
library(pscl)
```

    Warning message:
    "package 'pscl' was built under R version 3.6.3"Classes and Methods for R developed in the
    Political Science Computational Laboratory
    Department of Political Science
    Stanford University
    Simon Jackman
    hurdle and zeroinfl functions by Achim Zeileis



```R
pR2(logit.model) # 로지스틱회귀에서 R -squared 값으로만 모델이 적합한지 판단하기 어려움
```

    fitting null model for pseudo-r2


```R
	llh      -120.956334814774
	llhNull  -165.476660117844
	G2       89.0406506061402
	McFadden 0.269042928902269
	r2ML     0.30266438047444
	r2CU     0.410044303247615
```



```R
a$pre<-logit.model$fitted.values #예측값 컬럼 만들어줌
```


```R
a
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>marriage</th><th scope=col>edu</th><th scope=col>job</th><th scope=col>mincome</th><th scope=col>aware</th><th scope=col>count</th><th scope=col>amount</th><th scope=col>decision</th><th scope=col>propensity</th><th scope=col>skin</th><th scope=col>promo</th><th scope=col>location</th><th scope=col>satisf_b</th><th scope=col>satisf_i</th><th scope=col>satisf_al</th><th scope=col>repurchase</th><th scope=col>repurchase_re</th><th scope=col>pre</th></tr></thead>
<tbody>
	<tr><td>1         </td><td>1         </td><td>4         </td><td>1         </td><td>2         </td><td> 2        </td><td> 1        </td><td>  11000   </td><td>2         </td><td>1         </td><td>1         </td><td>1         </td><td>2         </td><td>5         </td><td>2         </td><td>2         </td><td>2         </td><td>0         </td><td>0.05228313</td></tr>
	<tr><td>2         </td><td>1         </td><td>4         </td><td>9         </td><td>2         </td><td> 1        </td><td> 4        </td><td>  30000   </td><td>1         </td><td>1         </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>1         </td><td>0.28577527</td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>4         </td><td>3         </td><td> 1        </td><td> 6        </td><td> 100000   </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>2         </td><td>4         </td><td>5         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>7         </td><td>5         </td><td> 2        </td><td> 6        </td><td>  65000   </td><td>3         </td><td>2         </td><td>5         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td></tr>
	<tr><td>1         </td><td>2         </td><td>6         </td><td>6         </td><td>5         </td><td> 2        </td><td> 2        </td><td>  50000   </td><td>2         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.31540893</td></tr>
	<tr><td>2         </td><td>2         </td><td>2         </td><td>7         </td><td>3         </td><td> 1        </td><td> 2        </td><td> 100000   </td><td>2         </td><td>1         </td><td>4         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>3         </td><td>0         </td><td>0.77972003</td></tr>
	<tr><td>2         </td><td>1         </td><td>6         </td><td>4         </td><td>5         </td><td> 1        </td><td> 5        </td><td> 100000   </td><td>3         </td><td>2         </td><td>5         </td><td>2         </td><td>3         </td><td>2         </td><td>2         </td><td>3         </td><td>4         </td><td>1         </td><td>0.38037538</td></tr>
	<tr><td>1         </td><td>1         </td><td>6         </td><td>4         </td><td>5         </td><td> 4        </td><td>10        </td><td>  39000   </td><td>3         </td><td>2         </td><td>2         </td><td>1         </td><td>2         </td><td>4         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>5         </td><td>2         </td><td> 2        </td><td> 2        </td><td>  40000   </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>5         </td><td>2         </td><td> 1        </td><td> 2        </td><td> 100000   </td><td>3         </td><td>3         </td><td>3         </td><td>1         </td><td>3         </td><td>2         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td></tr>
	<tr><td>2         </td><td>1         </td><td>7         </td><td>4         </td><td>3         </td><td>10        </td><td> 3        </td><td>  50000   </td><td>1         </td><td>3         </td><td>1         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.91476263</td></tr>
	<tr><td>1         </td><td>1         </td><td>2         </td><td>5         </td><td>3         </td><td> 2        </td><td> 1        </td><td>  30000   </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.38037538</td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>4         </td><td>3         </td><td> 4        </td><td> 4        </td><td> 320000   </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>2         </td><td>4         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.92219445</td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>4         </td><td>2         </td><td> 3        </td><td> 2        </td><td> 200000   </td><td>1         </td><td>2         </td><td>3         </td><td>1         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.29436649</td></tr>
	<tr><td>1         </td><td>2         </td><td>4         </td><td>4         </td><td>6         </td><td> 2        </td><td> 2        </td><td>  60000   </td><td>3         </td><td>2         </td><td>1         </td><td>2         </td><td>5         </td><td>3         </td><td>3         </td><td>3         </td><td>4         </td><td>1         </td><td>0.38037538</td></tr>
	<tr><td>2         </td><td>1         </td><td>4         </td><td>5         </td><td>2         </td><td> 2        </td><td> 3        </td><td>  50000   </td><td>1         </td><td>2         </td><td>4         </td><td>1         </td><td>3         </td><td>3         </td><td>4         </td><td>3         </td><td>3         </td><td>0         </td><td>0.29436649</td></tr>
	<tr><td>1         </td><td>2         </td><td>8         </td><td>3         </td><td>2         </td><td> 5        </td><td> 3        </td><td>1000000   </td><td>1         </td><td>3         </td><td>1         </td><td>2         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.57261194</td></tr>
	<tr><td>2         </td><td>1         </td><td>3         </td><td>8         </td><td>5         </td><td> 1        </td><td> 6        </td><td>1500000   </td><td>3         </td><td>3         </td><td>2         </td><td>4         </td><td>1         </td><td>4         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td></tr>
	<tr><td>1         </td><td>2         </td><td>2         </td><td>6         </td><td>2         </td><td> 4        </td><td> 1        </td><td>  80000   </td><td>2         </td><td>3         </td><td>1         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.92219445</td></tr>
	<tr><td>1         </td><td>1         </td><td>4         </td><td>4         </td><td>3         </td><td> 8        </td><td> 3        </td><td>  30000   </td><td>2         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.31540893</td></tr>
	<tr><td>2         </td><td>2         </td><td>2         </td><td>4         </td><td>2         </td><td> 8        </td><td> 4        </td><td> 350000   </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>2         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>7         </td><td>6         </td><td> 1        </td><td> 4        </td><td> 250000   </td><td>3         </td><td>3         </td><td>2         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>7         </td><td>3         </td><td> 1        </td><td>25        </td><td>  50000   </td><td>2         </td><td>2         </td><td>1         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.78680230</td></tr>
	<tr><td>2         </td><td>2         </td><td>2         </td><td>9         </td><td>1         </td><td> 1        </td><td> 1        </td><td>  20000   </td><td>1         </td><td>1         </td><td>5         </td><td>1         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.28577527</td></tr>
	<tr><td>1         </td><td>1         </td><td>3         </td><td>8         </td><td>4         </td><td> 2        </td><td> 3        </td><td>  42000   </td><td>1         </td><td>2         </td><td>3         </td><td>1         </td><td>3         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.76966815</td></tr>
	<tr><td>1         </td><td>1         </td><td>4         </td><td>8         </td><td>4         </td><td> 2        </td><td> 3        </td><td>  42000   </td><td>3         </td><td>3         </td><td>2         </td><td>1         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td></tr>
	<tr><td>2         </td><td>1         </td><td>4         </td><td>4         </td><td>3         </td><td> 2        </td><td>20        </td><td>  40000   </td><td>3         </td><td>1         </td><td>5         </td><td>2         </td><td>3         </td><td>2         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.82506274</td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>4         </td><td>6         </td><td> 1        </td><td> 6        </td><td>  70000   </td><td>3         </td><td>3         </td><td>5         </td><td>2         </td><td>1         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td></tr>
	<tr><td>2         </td><td>2         </td><td>8         </td><td>4         </td><td>5         </td><td> 5        </td><td> 6        </td><td> 200000   </td><td>2         </td><td>1         </td><td>4         </td><td>1         </td><td>1         </td><td>4         </td><td>4         </td><td>4         </td><td>3         </td><td>0         </td><td>0.77972003</td></tr>
	<tr><td>1         </td><td>2         </td><td>4         </td><td>2         </td><td>6         </td><td> 2        </td><td> 1        </td><td> 200000   </td><td>3         </td><td>2         </td><td>1         </td><td>2         </td><td>2         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 5         </td><td>2          </td><td>1          </td><td>10         </td><td>  30000    </td><td>2          </td><td>2          </td><td>2          </td><td>4          </td><td>3          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.786802301</td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 6         </td><td>6          </td><td>7          </td><td> 5         </td><td>  50000    </td><td>3          </td><td>2          </td><td>4          </td><td>2          </td><td>2          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.831003156</td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 1         </td><td>4          </td><td>1          </td><td> 1         </td><td>  10000    </td><td>1          </td><td>3          </td><td>1          </td><td>1          </td><td>1          </td><td>5          </td><td>1          </td><td>3          </td><td>1          </td><td>0          </td><td>0.572611941</td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 4         </td><td>3          </td><td>2          </td><td> 1         </td><td>  10000    </td><td>1          </td><td>1          </td><td>4          </td><td>3          </td><td>2          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.285775270</td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 4         </td><td>3          </td><td>2          </td><td> 3         </td><td>  50000    </td><td>3          </td><td>2          </td><td>5          </td><td>2          </td><td>3          </td><td>2          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.831003156</td></tr>
	<tr><td>1          </td><td>2          </td><td>2          </td><td> 5         </td><td>4          </td><td>2          </td><td> 1         </td><td>  60000    </td><td>1          </td><td>1          </td><td>1          </td><td>4          </td><td>5          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.285775270</td></tr>
	<tr><td>1          </td><td>1          </td><td>6          </td><td> 1         </td><td>3          </td><td>2          </td><td> 2         </td><td>  50000    </td><td>3          </td><td>2          </td><td>5          </td><td>1          </td><td>2          </td><td>4          </td><td>4          </td><td>3          </td><td>4          </td><td>1          </td><td>0.380375375</td></tr>
	<tr><td>2          </td><td>2          </td><td>6          </td><td> 4         </td><td>6          </td><td>1          </td><td> 3         </td><td> 500000    </td><td>3          </td><td>3          </td><td>5          </td><td>2          </td><td>1          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.940449868</td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 1         </td><td>3          </td><td>2          </td><td> 1         </td><td>  50000    </td><td>3          </td><td>2          </td><td>1          </td><td>2          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.380375375</td></tr>
	<tr><td>2          </td><td>2          </td><td>6          </td><td> 3         </td><td>4          </td><td>1          </td><td> 1         </td><td> 100000    </td><td>3          </td><td>2          </td><td>1          </td><td>2          </td><td>1          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.380375375</td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 7         </td><td>4          </td><td>1          </td><td> 2         </td><td>  50000    </td><td>3          </td><td>1          </td><td>5          </td><td>2          </td><td>2          </td><td>3          </td><td>2          </td><td>3          </td><td>4          </td><td>1          </td><td>0.370592284</td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 4         </td><td>2          </td><td>2          </td><td>12         </td><td>  20000    </td><td>2          </td><td>2          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.315408929</td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 4         </td><td>4          </td><td>2          </td><td> 4         </td><td>  35000    </td><td>2          </td><td>2          </td><td>1          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>4          </td><td>4          </td><td>1          </td><td>0.786802301</td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 4         </td><td>4          </td><td>2          </td><td> 4         </td><td>  30000    </td><td>2          </td><td>2          </td><td>1          </td><td>2          </td><td>3          </td><td>3          </td><td>4          </td><td>4          </td><td>3          </td><td>0          </td><td>0.786802301</td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 7         </td><td>1          </td><td>2          </td><td> 2         </td><td>  50000    </td><td>1          </td><td>1          </td><td>3          </td><td>2          </td><td>5          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.762188740</td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 7         </td><td>1          </td><td>2          </td><td> 3         </td><td>  50000    </td><td>1          </td><td>2          </td><td>3          </td><td>2          </td><td>5          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.294366491</td></tr>
	<tr><td>2          </td><td>1          </td><td>4          </td><td> 4         </td><td>2          </td><td>2          </td><td> 7         </td><td>  80000    </td><td>3          </td><td>2          </td><td>3          </td><td>2          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.380375375</td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 1         </td><td>3          </td><td>2          </td><td> 6         </td><td>  20000    </td><td>3          </td><td>1          </td><td>3          </td><td>1          </td><td>3          </td><td>2          </td><td>3          </td><td>4          </td><td>4          </td><td>1          </td><td>0.825062743</td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td>10         </td><td>2          </td><td>2          </td><td> 2         </td><td>  25000    </td><td>3          </td><td>1          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.825062743</td></tr>
	<tr><td>2          </td><td>1          </td><td>3          </td><td> 8         </td><td>1          </td><td>1          </td><td> 7         </td><td> 100000    </td><td>2          </td><td>1          </td><td>5          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>1          </td><td>2          </td><td>0          </td><td>0.006840090</td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 4         </td><td>3          </td><td>2          </td><td> 2         </td><td>  50000    </td><td>1          </td><td>2          </td><td>5          </td><td>2          </td><td>2          </td><td>3          </td><td>4          </td><td>3          </td><td>3          </td><td>0          </td><td>0.294366491</td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 4         </td><td>5          </td><td>2          </td><td> 1         </td><td>  80000    </td><td>2          </td><td>2          </td><td>1          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.315408929</td></tr>
	<tr><td>2          </td><td>2          </td><td>6          </td><td> 7         </td><td>5          </td><td>1          </td><td> 2         </td><td> 300000    </td><td>3          </td><td>3          </td><td>2          </td><td>3          </td><td>2          </td><td>3          </td><td>4          </td><td>3          </td><td>3          </td><td>0          </td><td>0.663477744</td></tr>
	<tr><td>2          </td><td>2          </td><td>7          </td><td> 7         </td><td>4          </td><td>1          </td><td> 2         </td><td> 200000    </td><td>2          </td><td>2          </td><td>4          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.315408929</td></tr>
	<tr><td>1          </td><td>1          </td><td>2          </td><td> 1         </td><td>2          </td><td>2          </td><td> 5         </td><td>   3000    </td><td>1          </td><td>1          </td><td>2          </td><td>1          </td><td>1          </td><td>1          </td><td>1          </td><td>1          </td><td>1          </td><td>0          </td><td>0.006197395</td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 2         </td><td>3          </td><td>2          </td><td> 6         </td><td>   4000    </td><td>1          </td><td>1          </td><td>1          </td><td>1          </td><td>4          </td><td>2          </td><td>1          </td><td>1          </td><td>1          </td><td>0          </td><td>0.006197395</td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 4         </td><td>2          </td><td>1          </td><td>10         </td><td> 150000    </td><td>3          </td><td>2          </td><td>2          </td><td>1          </td><td>2          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.831003156</td></tr>
	<tr><td>2          </td><td>2          </td><td>7          </td><td> 8         </td><td>1          </td><td>2          </td><td> 3         </td><td> 100000    </td><td>1          </td><td>2          </td><td>1          </td><td>1          </td><td>5          </td><td>2          </td><td>5          </td><td>4          </td><td>4          </td><td>1          </td><td>0.769668153</td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 6         </td><td>1          </td><td>3          </td><td> 2         </td><td>  20000    </td><td>3          </td><td>1          </td><td>1          </td><td>1          </td><td>3          </td><td>4          </td><td>3          </td><td>3          </td><td>2          </td><td>0          </td><td>0.370592284</td></tr>
	<tr><td>2          </td><td>2          </td><td>6          </td><td>10         </td><td>1          </td><td>1          </td><td>10         </td><td>1000000    </td><td>3          </td><td>2          </td><td>3          </td><td>1          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.380375375</td></tr>
</tbody>
</table>



- 0.5 기준(50%)으로 구매할지 안할지 예측한다,

- pre 컬럼값 중 0.5 보다 크면 구매 할 확률이 높고 0.5 보다 낮으면 구매 할 확률이 낫다,


```R
a$preCroup <-recode(a$pre, "lo:0.5=0; 0.5:hi=1")
```


```R
table(a$preCroup)
```



      0   1
    105 142



```R
a
```


<table>
<thead><tr><th scope=col>gender</th><th scope=col>marriage</th><th scope=col>edu</th><th scope=col>job</th><th scope=col>mincome</th><th scope=col>aware</th><th scope=col>count</th><th scope=col>amount</th><th scope=col>decision</th><th scope=col>propensity</th><th scope=col>skin</th><th scope=col>promo</th><th scope=col>location</th><th scope=col>satisf_b</th><th scope=col>satisf_i</th><th scope=col>satisf_al</th><th scope=col>repurchase</th><th scope=col>repurchase_re</th><th scope=col>pre</th><th scope=col>preCroup</th></tr></thead>
<tbody>
	<tr><td>1         </td><td>1         </td><td>4         </td><td>1         </td><td>2         </td><td> 2        </td><td> 1        </td><td>  11000   </td><td>2         </td><td>1         </td><td>1         </td><td>1         </td><td>2         </td><td>5         </td><td>2         </td><td>2         </td><td>2         </td><td>0         </td><td>0.05228313</td><td>0         </td></tr>
	<tr><td>2         </td><td>1         </td><td>4         </td><td>9         </td><td>2         </td><td> 1        </td><td> 4        </td><td>  30000   </td><td>1         </td><td>1         </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>1         </td><td>0.28577527</td><td>0         </td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>4         </td><td>3         </td><td> 1        </td><td> 6        </td><td> 100000   </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>2         </td><td>4         </td><td>5         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td><td>1         </td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>7         </td><td>5         </td><td> 2        </td><td> 6        </td><td>  65000   </td><td>3         </td><td>2         </td><td>5         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td><td>1         </td></tr>
	<tr><td>1         </td><td>2         </td><td>6         </td><td>6         </td><td>5         </td><td> 2        </td><td> 2        </td><td>  50000   </td><td>2         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.31540893</td><td>0         </td></tr>
	<tr><td>2         </td><td>2         </td><td>2         </td><td>7         </td><td>3         </td><td> 1        </td><td> 2        </td><td> 100000   </td><td>2         </td><td>1         </td><td>4         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>3         </td><td>0         </td><td>0.77972003</td><td>1         </td></tr>
	<tr><td>2         </td><td>1         </td><td>6         </td><td>4         </td><td>5         </td><td> 1        </td><td> 5        </td><td> 100000   </td><td>3         </td><td>2         </td><td>5         </td><td>2         </td><td>3         </td><td>2         </td><td>2         </td><td>3         </td><td>4         </td><td>1         </td><td>0.38037538</td><td>0         </td></tr>
	<tr><td>1         </td><td>1         </td><td>6         </td><td>4         </td><td>5         </td><td> 4        </td><td>10        </td><td>  39000   </td><td>3         </td><td>2         </td><td>2         </td><td>1         </td><td>2         </td><td>4         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td><td>1         </td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>5         </td><td>2         </td><td> 2        </td><td> 2        </td><td>  40000   </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td><td>1         </td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>5         </td><td>2         </td><td> 1        </td><td> 2        </td><td> 100000   </td><td>3         </td><td>3         </td><td>3         </td><td>1         </td><td>3         </td><td>2         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td><td>1         </td></tr>
	<tr><td>2         </td><td>1         </td><td>7         </td><td>4         </td><td>3         </td><td>10        </td><td> 3        </td><td>  50000   </td><td>1         </td><td>3         </td><td>1         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.91476263</td><td>1         </td></tr>
	<tr><td>1         </td><td>1         </td><td>2         </td><td>5         </td><td>3         </td><td> 2        </td><td> 1        </td><td>  30000   </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.38037538</td><td>0         </td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>4         </td><td>3         </td><td> 4        </td><td> 4        </td><td> 320000   </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>2         </td><td>4         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.92219445</td><td>1         </td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>4         </td><td>2         </td><td> 3        </td><td> 2        </td><td> 200000   </td><td>1         </td><td>2         </td><td>3         </td><td>1         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.29436649</td><td>0         </td></tr>
	<tr><td>1         </td><td>2         </td><td>4         </td><td>4         </td><td>6         </td><td> 2        </td><td> 2        </td><td>  60000   </td><td>3         </td><td>2         </td><td>1         </td><td>2         </td><td>5         </td><td>3         </td><td>3         </td><td>3         </td><td>4         </td><td>1         </td><td>0.38037538</td><td>0         </td></tr>
	<tr><td>2         </td><td>1         </td><td>4         </td><td>5         </td><td>2         </td><td> 2        </td><td> 3        </td><td>  50000   </td><td>1         </td><td>2         </td><td>4         </td><td>1         </td><td>3         </td><td>3         </td><td>4         </td><td>3         </td><td>3         </td><td>0         </td><td>0.29436649</td><td>0         </td></tr>
	<tr><td>1         </td><td>2         </td><td>8         </td><td>3         </td><td>2         </td><td> 5        </td><td> 3        </td><td>1000000   </td><td>1         </td><td>3         </td><td>1         </td><td>2         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.57261194</td><td>1         </td></tr>
	<tr><td>2         </td><td>1         </td><td>3         </td><td>8         </td><td>5         </td><td> 1        </td><td> 6        </td><td>1500000   </td><td>3         </td><td>3         </td><td>2         </td><td>4         </td><td>1         </td><td>4         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td><td>1         </td></tr>
	<tr><td>1         </td><td>2         </td><td>2         </td><td>6         </td><td>2         </td><td> 4        </td><td> 1        </td><td>  80000   </td><td>2         </td><td>3         </td><td>1         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.92219445</td><td>1         </td></tr>
	<tr><td>1         </td><td>1         </td><td>4         </td><td>4         </td><td>3         </td><td> 8        </td><td> 3        </td><td>  30000   </td><td>2         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.31540893</td><td>0         </td></tr>
	<tr><td>2         </td><td>2         </td><td>2         </td><td>4         </td><td>2         </td><td> 8        </td><td> 4        </td><td> 350000   </td><td>3         </td><td>2         </td><td>3         </td><td>2         </td><td>2         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td><td>1         </td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>7         </td><td>6         </td><td> 1        </td><td> 4        </td><td> 250000   </td><td>3         </td><td>3         </td><td>2         </td><td>2         </td><td>3         </td><td>2         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td><td>1         </td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>7         </td><td>3         </td><td> 1        </td><td>25        </td><td>  50000   </td><td>2         </td><td>2         </td><td>1         </td><td>2         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.78680230</td><td>1         </td></tr>
	<tr><td>2         </td><td>2         </td><td>2         </td><td>9         </td><td>1         </td><td> 1        </td><td> 1        </td><td>  20000   </td><td>1         </td><td>1         </td><td>5         </td><td>1         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>3         </td><td>0         </td><td>0.28577527</td><td>0         </td></tr>
	<tr><td>1         </td><td>1         </td><td>3         </td><td>8         </td><td>4         </td><td> 2        </td><td> 3        </td><td>  42000   </td><td>1         </td><td>2         </td><td>3         </td><td>1         </td><td>3         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>1         </td><td>0.76966815</td><td>1         </td></tr>
	<tr><td>1         </td><td>1         </td><td>4         </td><td>8         </td><td>4         </td><td> 2        </td><td> 3        </td><td>  42000   </td><td>3         </td><td>3         </td><td>2         </td><td>1         </td><td>3         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td><td>1         </td></tr>
	<tr><td>2         </td><td>1         </td><td>4         </td><td>4         </td><td>3         </td><td> 2        </td><td>20        </td><td>  40000   </td><td>3         </td><td>1         </td><td>5         </td><td>2         </td><td>3         </td><td>2         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.82506274</td><td>1         </td></tr>
	<tr><td>2         </td><td>2         </td><td>4         </td><td>4         </td><td>6         </td><td> 1        </td><td> 6        </td><td>  70000   </td><td>3         </td><td>3         </td><td>5         </td><td>2         </td><td>1         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.94044987</td><td>1         </td></tr>
	<tr><td>2         </td><td>2         </td><td>8         </td><td>4         </td><td>5         </td><td> 5        </td><td> 6        </td><td> 200000   </td><td>2         </td><td>1         </td><td>4         </td><td>1         </td><td>1         </td><td>4         </td><td>4         </td><td>4         </td><td>3         </td><td>0         </td><td>0.77972003</td><td>1         </td></tr>
	<tr><td>1         </td><td>2         </td><td>4         </td><td>2         </td><td>6         </td><td> 2        </td><td> 1        </td><td> 200000   </td><td>3         </td><td>2         </td><td>1         </td><td>2         </td><td>2         </td><td>3         </td><td>4         </td><td>4         </td><td>4         </td><td>1         </td><td>0.83100316</td><td>1         </td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 5         </td><td>2          </td><td>1          </td><td>10         </td><td>  30000    </td><td>2          </td><td>2          </td><td>2          </td><td>4          </td><td>3          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.786802301</td><td>1          </td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 6         </td><td>6          </td><td>7          </td><td> 5         </td><td>  50000    </td><td>3          </td><td>2          </td><td>4          </td><td>2          </td><td>2          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.831003156</td><td>1          </td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 1         </td><td>4          </td><td>1          </td><td> 1         </td><td>  10000    </td><td>1          </td><td>3          </td><td>1          </td><td>1          </td><td>1          </td><td>5          </td><td>1          </td><td>3          </td><td>1          </td><td>0          </td><td>0.572611941</td><td>1          </td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 4         </td><td>3          </td><td>2          </td><td> 1         </td><td>  10000    </td><td>1          </td><td>1          </td><td>4          </td><td>3          </td><td>2          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.285775270</td><td>0          </td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 4         </td><td>3          </td><td>2          </td><td> 3         </td><td>  50000    </td><td>3          </td><td>2          </td><td>5          </td><td>2          </td><td>3          </td><td>2          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.831003156</td><td>1          </td></tr>
	<tr><td>1          </td><td>2          </td><td>2          </td><td> 5         </td><td>4          </td><td>2          </td><td> 1         </td><td>  60000    </td><td>1          </td><td>1          </td><td>1          </td><td>4          </td><td>5          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.285775270</td><td>0          </td></tr>
	<tr><td>1          </td><td>1          </td><td>6          </td><td> 1         </td><td>3          </td><td>2          </td><td> 2         </td><td>  50000    </td><td>3          </td><td>2          </td><td>5          </td><td>1          </td><td>2          </td><td>4          </td><td>4          </td><td>3          </td><td>4          </td><td>1          </td><td>0.380375375</td><td>0          </td></tr>
	<tr><td>2          </td><td>2          </td><td>6          </td><td> 4         </td><td>6          </td><td>1          </td><td> 3         </td><td> 500000    </td><td>3          </td><td>3          </td><td>5          </td><td>2          </td><td>1          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.940449868</td><td>1          </td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 1         </td><td>3          </td><td>2          </td><td> 1         </td><td>  50000    </td><td>3          </td><td>2          </td><td>1          </td><td>2          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.380375375</td><td>0          </td></tr>
	<tr><td>2          </td><td>2          </td><td>6          </td><td> 3         </td><td>4          </td><td>1          </td><td> 1         </td><td> 100000    </td><td>3          </td><td>2          </td><td>1          </td><td>2          </td><td>1          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.380375375</td><td>0          </td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 7         </td><td>4          </td><td>1          </td><td> 2         </td><td>  50000    </td><td>3          </td><td>1          </td><td>5          </td><td>2          </td><td>2          </td><td>3          </td><td>2          </td><td>3          </td><td>4          </td><td>1          </td><td>0.370592284</td><td>0          </td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 4         </td><td>2          </td><td>2          </td><td>12         </td><td>  20000    </td><td>2          </td><td>2          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.315408929</td><td>0          </td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 4         </td><td>4          </td><td>2          </td><td> 4         </td><td>  35000    </td><td>2          </td><td>2          </td><td>1          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>4          </td><td>4          </td><td>1          </td><td>0.786802301</td><td>1          </td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 4         </td><td>4          </td><td>2          </td><td> 4         </td><td>  30000    </td><td>2          </td><td>2          </td><td>1          </td><td>2          </td><td>3          </td><td>3          </td><td>4          </td><td>4          </td><td>3          </td><td>0          </td><td>0.786802301</td><td>1          </td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 7         </td><td>1          </td><td>2          </td><td> 2         </td><td>  50000    </td><td>1          </td><td>1          </td><td>3          </td><td>2          </td><td>5          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.762188740</td><td>1          </td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 7         </td><td>1          </td><td>2          </td><td> 3         </td><td>  50000    </td><td>1          </td><td>2          </td><td>3          </td><td>2          </td><td>5          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.294366491</td><td>0          </td></tr>
	<tr><td>2          </td><td>1          </td><td>4          </td><td> 4         </td><td>2          </td><td>2          </td><td> 7         </td><td>  80000    </td><td>3          </td><td>2          </td><td>3          </td><td>2          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.380375375</td><td>0          </td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 1         </td><td>3          </td><td>2          </td><td> 6         </td><td>  20000    </td><td>3          </td><td>1          </td><td>3          </td><td>1          </td><td>3          </td><td>2          </td><td>3          </td><td>4          </td><td>4          </td><td>1          </td><td>0.825062743</td><td>1          </td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td>10         </td><td>2          </td><td>2          </td><td> 2         </td><td>  25000    </td><td>3          </td><td>1          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.825062743</td><td>1          </td></tr>
	<tr><td>2          </td><td>1          </td><td>3          </td><td> 8         </td><td>1          </td><td>1          </td><td> 7         </td><td> 100000    </td><td>2          </td><td>1          </td><td>5          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>1          </td><td>2          </td><td>0          </td><td>0.006840090</td><td>0          </td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 4         </td><td>3          </td><td>2          </td><td> 2         </td><td>  50000    </td><td>1          </td><td>2          </td><td>5          </td><td>2          </td><td>2          </td><td>3          </td><td>4          </td><td>3          </td><td>3          </td><td>0          </td><td>0.294366491</td><td>0          </td></tr>
	<tr><td>1          </td><td>2          </td><td>4          </td><td> 4         </td><td>5          </td><td>2          </td><td> 1         </td><td>  80000    </td><td>2          </td><td>2          </td><td>1          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.315408929</td><td>0          </td></tr>
	<tr><td>2          </td><td>2          </td><td>6          </td><td> 7         </td><td>5          </td><td>1          </td><td> 2         </td><td> 300000    </td><td>3          </td><td>3          </td><td>2          </td><td>3          </td><td>2          </td><td>3          </td><td>4          </td><td>3          </td><td>3          </td><td>0          </td><td>0.663477744</td><td>1          </td></tr>
	<tr><td>2          </td><td>2          </td><td>7          </td><td> 7         </td><td>4          </td><td>1          </td><td> 2         </td><td> 200000    </td><td>2          </td><td>2          </td><td>4          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.315408929</td><td>0          </td></tr>
	<tr><td>1          </td><td>1          </td><td>2          </td><td> 1         </td><td>2          </td><td>2          </td><td> 5         </td><td>   3000    </td><td>1          </td><td>1          </td><td>2          </td><td>1          </td><td>1          </td><td>1          </td><td>1          </td><td>1          </td><td>1          </td><td>0          </td><td>0.006197395</td><td>0          </td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 2         </td><td>3          </td><td>2          </td><td> 6         </td><td>   4000    </td><td>1          </td><td>1          </td><td>1          </td><td>1          </td><td>4          </td><td>2          </td><td>1          </td><td>1          </td><td>1          </td><td>0          </td><td>0.006197395</td><td>0          </td></tr>
	<tr><td>2          </td><td>2          </td><td>4          </td><td> 4         </td><td>2          </td><td>1          </td><td>10         </td><td> 150000    </td><td>3          </td><td>2          </td><td>2          </td><td>1          </td><td>2          </td><td>3          </td><td>4          </td><td>4          </td><td>4          </td><td>1          </td><td>0.831003156</td><td>1          </td></tr>
	<tr><td>2          </td><td>2          </td><td>7          </td><td> 8         </td><td>1          </td><td>2          </td><td> 3         </td><td> 100000    </td><td>1          </td><td>2          </td><td>1          </td><td>1          </td><td>5          </td><td>2          </td><td>5          </td><td>4          </td><td>4          </td><td>1          </td><td>0.769668153</td><td>1          </td></tr>
	<tr><td>1          </td><td>1          </td><td>4          </td><td> 6         </td><td>1          </td><td>3          </td><td> 2         </td><td>  20000    </td><td>3          </td><td>1          </td><td>1          </td><td>1          </td><td>3          </td><td>4          </td><td>3          </td><td>3          </td><td>2          </td><td>0          </td><td>0.370592284</td><td>0          </td></tr>
	<tr><td>2          </td><td>2          </td><td>6          </td><td>10         </td><td>1          </td><td>1          </td><td>10         </td><td>1000000    </td><td>3          </td><td>2          </td><td>3          </td><td>1          </td><td>3          </td><td>2          </td><td>3          </td><td>3          </td><td>3          </td><td>0          </td><td>0.380375375</td><td>0          </td></tr>
</tbody>
</table>



- 두번째 행은 예측하는데 실패함 (1으로 실제 구매했는데 (0.28577527 확률) 0으로 예측함)


```R
model.fit1<-predict(logit.model, type='response')
# response 는 결과 종속변수를 저장하라는 의미임
```


```R
model.fit2<-ifelse(model.fit1 >0.5,1,0)
```


```R
model.fit3<-data.frame(model.fit2)
```


```R
model.fit3
```


<table>
<thead><tr><th scope=col>model.fit2</th></tr></thead>
<tbody>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>...</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
	<tr><td>1</td></tr>
	<tr><td>1</td></tr>
	<tr><td>0</td></tr>
	<tr><td>0</td></tr>
</tbody>
</table>




```R
library(gmodels) #교차분석하기위해 사용함
```

    Warning message:
    "package 'gmodels' was built under R version 3.6.3"


```R
CrossTable(x=a$repurchase_re, y=model.fit3$model.fit2, chisq=T)
```



       Cell Contents
    |-------------------------|
    |                       N |
    | Chi-square contribution |
    |           N / Row Total |
    |           N / Col Total |
    |         N / Table Total |
    |-------------------------|


    Total Observations in Table:  247


                    | model.fit3$model.fit2
    a$repurchase_re |         0 |         1 | Row Total |
    ----------------|-----------|-----------|-----------|
                  0 |        77 |        20 |        97 |
                    |    31.021 |    22.938 |           |
                    |     0.794 |     0.206 |     0.393 |
                    |     0.733 |     0.141 |           |
                    |     0.312 |     0.081 |           |
    ----------------|-----------|-----------|-----------|
                  1 |        28 |       122 |       150 |
                    |    20.060 |    14.833 |           |
                    |     0.187 |     0.813 |     0.607 |
                    |     0.267 |     0.859 |           |
                    |     0.113 |     0.494 |           |
    ----------------|-----------|-----------|-----------|
       Column Total |       105 |       142 |       247 |
                    |     0.425 |     0.575 |           |
    ----------------|-----------|-----------|-----------|


    Statistics for All Table Factors


    Pearson's Chi-squared test
    ------------------------------------------------------------
    Chi^2 =  88.8528     d.f. =  1     p =  4.253117e-21

    Pearson's Chi-squared test with Yates' continuity correction
    ------------------------------------------------------------
    Chi^2 =  86.38583     d.f. =  1     p =  1.480422e-20




- 실제 값과 예측 값의 교차표이다.

- 구매안했는데 구매할 것으로 예측한 비율이 대략 20%이다.

- 전체 값 247중에 예측으로 맞은 값 77+122 = 199 이다. 199/247 = 0.805668016194332, 대략 80%의 정확도를 보여준다.


```R
library(ROCR)
```


```R
pr <- prediction(model.fit1, a$repurchase_re)
```


```R
prf<-performance(pr,measure = 'tpr', x.measure = 'fpr')
```


```R
plot(prf) #ROC 커브를 그려줌
```



![output_50_0]({{ site.gdrive_url_prefix }}1YBWy5kqwkjF8wkq7P0Ch0qcDA6NK23_K)


- 볼록 뛰어나올수록 예측확률이 높다는 것이다. 위그래프는 볼록 뛰어나왔다.


```R
auc<-performance(pr,measure = 'auc')
```


```R
auc<-auc@y.values[[1]]
```


```R
auc # 대략적으로 0.7이상이면 예측 확률이 좋다는 것이다.
```


0.848213058419244

