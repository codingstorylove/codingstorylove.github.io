---
layout: single
title: "오차행렬로 모델 평가"
date: "2022-01-27 16:20:00 +0900"
last_modified_at: "2022-01-27 18:36:15 +0900"
---

# 오차행렬로 모델 평가

- 훈련데이터의 정확도와 테스트데이터의 정확도가 높아야 하고 차이가 적을수록 좋은 모델이다.(일반화 가능성이 높은 결과)

- 실제와 예측이 얼마나 일치하는가라는 관점이 모델의 성능을 평가할때 가장 중요함

> 평가할때 오차행렬(confusion matrix) 사용함 

1. 0을 0으로 1을 1로 예측하는 정확도가 중요함 

2. 오차 행렬 평가지표



![confusionmatrix1]({{ site.gdrive_url_prefix }}1JY0oLVr7qVo_DClOYdP7OH5jIY8SiKQ-)


오차 행렬은 위와 같이 4분면 행렬에서 실제 label class과 예측 label class가 어떠한 유형을 가지고 mapping 되는지 나타낸다. 4분면의 왼쪽과 오른쪽은 에측된 class를 기준으로 Negative와 Positive로 분류하고, 위와 아래는 실제 class를 기준으로 Negative와 Positive로 분류한다. 따라서 예측 class와 실제 class에 따라 TN, FP, FN, TP 형태로 오차 행렬의 4분면을 채울 수 있다.

1. TN은 예측값을 Negative 값인 0으로 예측했고 실제값 또한 Negative 값인 0일 때

2. FP은 예측값을 Positive 값인 1으로 예측했는데 실제값은 Negative 값인 0일 때

3. FN은 예측값을 Negative 값인 0으로 예측했는데 실제값은 Positive 값인 1일 때

4. TP은 예측값을 Positive 값인 1으로 예측했고 실제값 또한 Positive 값인 1일 때

> 정밀도(precision) = TP/(TP+FP)

> 재현율(recall) = TP/(TP+FN)

> F-score = 2/ ((1/정밀도) + (1/재현율))

# 실습


```R
data<-read.csv("data/Fvote.csv", header=TRUE)
data<-data[ , 2:15]
data
```


<table>
<thead><tr><th scope=col>gender.male</th><th scope=col>gender.female</th><th scope=col>region.Sudo</th><th scope=col>region.Chungcheung</th><th scope=col>region.Honam</th><th scope=col>region.Youngnam</th><th scope=col>region.Others</th><th scope=col>edu</th><th scope=col>income</th><th scope=col>age</th><th scope=col>score_gov</th><th scope=col>score_progress</th><th scope=col>score_intention</th><th scope=col>vote</th></tr></thead>
<tbody>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>1.0      </td><td>0.6666667</td><td>0.6666667</td><td>0.25     </td><td>0.25     </td><td>0.75     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0.5      </td><td>0.6666667</td><td>0.6666667</td><td>0.25     </td><td>0.75     </td><td>0.50     </td><td>0        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.00     </td><td>0.50     </td><td>0.45     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.6666667</td><td>1.00     </td><td>0.75     </td><td>0.40     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.75     </td><td>0.50     </td><td>0.35     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.00     </td><td>0.75     </td><td>0.70     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.75     </td><td>0.75     </td><td>0.25     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0.5      </td><td>1.0000000</td><td>1.0000000</td><td>0.50     </td><td>0.75     </td><td>0.65     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.25     </td><td>0.25     </td><td>0.25     </td><td>0        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>0.6666667</td><td>0.75     </td><td>0.25     </td><td>0.50     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>0.6666667</td><td>0.25     </td><td>0.75     </td><td>0.30     </td><td>0        </td></tr>
	<tr><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.6666667</td><td>0.50     </td><td>0.25     </td><td>0.40     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.50     </td><td>0.25     </td><td>0.50     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>1.0000000</td><td>1.0000000</td><td>0.50     </td><td>0.50     </td><td>0.35     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>1.0      </td><td>1.0000000</td><td>0.6666667</td><td>0.50     </td><td>0.75     </td><td>0.65     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.6666667</td><td>0.6666667</td><td>0.50     </td><td>0.50     </td><td>0.55     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0.5      </td><td>1.0000000</td><td>0.6666667</td><td>0.75     </td><td>0.50     </td><td>0.75     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.3333333</td><td>0.6666667</td><td>1.00     </td><td>0.75     </td><td>0.40     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>0.3333333</td><td>0.50     </td><td>0.25     </td><td>0.50     </td><td>0        </td></tr>
	<tr><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>1.0      </td><td>1.0000000</td><td>0.6666667</td><td>0.50     </td><td>0.50     </td><td>0.50     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.3333333</td><td>0.50     </td><td>0.50     </td><td>0.25     </td><td>0        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1.0      </td><td>0.3333333</td><td>0.3333333</td><td>0.50     </td><td>0.75     </td><td>0.30     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0.5      </td><td>0.3333333</td><td>0.3333333</td><td>0.50     </td><td>0.50     </td><td>0.10     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.6666667</td><td>0.6666667</td><td>0.50     </td><td>0.75     </td><td>0.15     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0.5      </td><td>0.6666667</td><td>0.6666667</td><td>0.50     </td><td>0.25     </td><td>0.65     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.3333333</td><td>0.3333333</td><td>0.50     </td><td>0.25     </td><td>0.55     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.6666667</td><td>0.3333333</td><td>0.50     </td><td>0.75     </td><td>0.45     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>1.0000000</td><td>0.6666667</td><td>0.75     </td><td>0.75     </td><td>0.50     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.6666667</td><td>0.6666667</td><td>0.00     </td><td>0.25     </td><td>0.50     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>1.0000000</td><td>1.0000000</td><td>0.50     </td><td>0.50     </td><td>0.30     </td><td>1        </td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.3333333</td><td>0.50     </td><td>1.00     </td><td>0.30     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.3333333</td><td>0.75     </td><td>0.50     </td><td>0.60     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.0000000</td><td>0.75     </td><td>0.25     </td><td>0.60     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.3333333</td><td>0.00     </td><td>0.75     </td><td>0.45     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.0000000</td><td>0.50     </td><td>0.75     </td><td>0.45     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.3333333</td><td>0.25     </td><td>0.75     </td><td>0.50     </td><td>0        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0.5      </td><td>0.3333333</td><td>0.6666667</td><td>0.75     </td><td>0.50     </td><td>0.60     </td><td>0        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.0000000</td><td>0.50     </td><td>0.50     </td><td>0.45     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.3333333</td><td>0.50     </td><td>0.50     </td><td>0.50     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0.0      </td><td>0.0000000</td><td>0.0000000</td><td>0.25     </td><td>0.25     </td><td>0.40     </td><td>0        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.6666667</td><td>0.75     </td><td>0.75     </td><td>0.85     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.0000000</td><td>0.50     </td><td>0.75     </td><td>0.45     </td><td>0        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0.0      </td><td>0.0000000</td><td>0.3333333</td><td>0.50     </td><td>0.75     </td><td>0.60     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.3333333</td><td>0.25     </td><td>0.00     </td><td>0.30     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0.0      </td><td>0.0000000</td><td>0.0000000</td><td>0.50     </td><td>0.50     </td><td>0.50     </td><td>0        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.3333333</td><td>0.6666667</td><td>0.25     </td><td>0.75     </td><td>0.50     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.6666667</td><td>0.25     </td><td>0.50     </td><td>0.50     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.3333333</td><td>0.75     </td><td>0.75     </td><td>1.00     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.3333333</td><td>0.50     </td><td>0.25     </td><td>0.30     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.3333333</td><td>0.75     </td><td>0.50     </td><td>0.50     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.3333333</td><td>0.75     </td><td>0.75     </td><td>0.65     </td><td>0        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.3333333</td><td>0.50     </td><td>0.50     </td><td>0.60     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.3333333</td><td>0.3333333</td><td>0.50     </td><td>0.50     </td><td>0.65     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.0000000</td><td>0.0000000</td><td>1.00     </td><td>0.75     </td><td>0.55     </td><td>0        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.6666667</td><td>1.0000000</td><td>0.50     </td><td>0.25     </td><td>0.00     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0.0      </td><td>1.0000000</td><td>1.0000000</td><td>0.50     </td><td>0.50     </td><td>0.20     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.3333333</td><td>0.50     </td><td>0.75     </td><td>0.40     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.3333333</td><td>0.50     </td><td>0.50     </td><td>0.40     </td><td>1        </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.6666667</td><td>1.0000000</td><td>0.50     </td><td>0.25     </td><td>0.75     </td><td>1        </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.3333333</td><td>0.3333333</td><td>0.50     </td><td>0.50     </td><td>0.70     </td><td>1        </td></tr>
</tbody>
</table>




```R
data$vote<-factor(data$vote, labels=c("no", "yes"))
head(data)
```


<table>
<thead><tr><th scope=col>gender.male</th><th scope=col>gender.female</th><th scope=col>region.Sudo</th><th scope=col>region.Chungcheung</th><th scope=col>region.Honam</th><th scope=col>region.Youngnam</th><th scope=col>region.Others</th><th scope=col>edu</th><th scope=col>income</th><th scope=col>age</th><th scope=col>score_gov</th><th scope=col>score_progress</th><th scope=col>score_intention</th><th scope=col>vote</th></tr></thead>
<tbody>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>1.0      </td><td>0.6666667</td><td>0.6666667</td><td>0.25     </td><td>0.25     </td><td>0.75     </td><td>yes      </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0.5      </td><td>0.6666667</td><td>0.6666667</td><td>0.25     </td><td>0.75     </td><td>0.50     </td><td>no       </td></tr>
	<tr><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.00     </td><td>0.50     </td><td>0.45     </td><td>yes      </td></tr>
	<tr><td>0        </td><td>1        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.5      </td><td>0.0000000</td><td>0.6666667</td><td>1.00     </td><td>0.75     </td><td>0.40     </td><td>yes      </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.75     </td><td>0.50     </td><td>0.35     </td><td>yes      </td></tr>
	<tr><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>0        </td><td>0        </td><td>0.0      </td><td>0.3333333</td><td>1.0000000</td><td>0.00     </td><td>0.75     </td><td>0.70     </td><td>yes      </td></tr>
</tbody>
</table>



## train / test dataset 나누기



```R
library(caret)

```


```R
set.seed(42)
# vote를 종속변수로 두기 (투표를 했는지 안했는지 예측)
training.samples <- createDataPartition(data$vote, p = 0.7, list = FALSE)
training.samples
```


<table>
<thead><tr><th scope=col>Resample1</th></tr></thead>
<tbody>
	<tr><td> 1</td></tr>
	<tr><td> 2</td></tr>
	<tr><td> 3</td></tr>
	<tr><td> 4</td></tr>
	<tr><td> 5</td></tr>
	<tr><td> 6</td></tr>
	<tr><td> 7</td></tr>
	<tr><td>11</td></tr>
	<tr><td>12</td></tr>
	<tr><td>15</td></tr>
	<tr><td>16</td></tr>
	<tr><td>17</td></tr>
	<tr><td>18</td></tr>
	<tr><td>19</td></tr>
	<tr><td>20</td></tr>
	<tr><td>21</td></tr>
	<tr><td>23</td></tr>
	<tr><td>25</td></tr>
	<tr><td>26</td></tr>
	<tr><td>27</td></tr>
	<tr><td>28</td></tr>
	<tr><td>29</td></tr>
	<tr><td>30</td></tr>
	<tr><td>31</td></tr>
	<tr><td>35</td></tr>
	<tr><td>36</td></tr>
	<tr><td>37</td></tr>
	<tr><td>38</td></tr>
	<tr><td>39</td></tr>
	<tr><td>40</td></tr>
	<tr><td>...</td></tr>
	<tr><td>170</td></tr>
	<tr><td>171</td></tr>
	<tr><td>173</td></tr>
	<tr><td>174</td></tr>
	<tr><td>175</td></tr>
	<tr><td>176</td></tr>
	<tr><td>177</td></tr>
	<tr><td>178</td></tr>
	<tr><td>179</td></tr>
	<tr><td>181</td></tr>
	<tr><td>183</td></tr>
	<tr><td>184</td></tr>
	<tr><td>187</td></tr>
	<tr><td>188</td></tr>
	<tr><td>189</td></tr>
	<tr><td>190</td></tr>
	<tr><td>191</td></tr>
	<tr><td>192</td></tr>
	<tr><td>193</td></tr>
	<tr><td>195</td></tr>
	<tr><td>196</td></tr>
	<tr><td>197</td></tr>
	<tr><td>198</td></tr>
	<tr><td>200</td></tr>
	<tr><td>201</td></tr>
	<tr><td>202</td></tr>
	<tr><td>205</td></tr>
	<tr><td>207</td></tr>
	<tr><td>208</td></tr>
	<tr><td>210</td></tr>
</tbody>
</table>




```R

train  <- data[training.samples, ]
test <- data[-training.samples, ]
```


```R
finalControl <- trainControl(method = "none", classProbs = TRUE)
FinalModel <- train(vote ~ ., data = train, 
                    method = "knn", 
                    trControl = finalControl, 
                    tuneGrid = data.frame(k=8),
                    metric = "Accuracy")
FinalModel
```


    k-Nearest Neighbors 
    
    148 samples
     13 predictor
      2 classes: 'no', 'yes' 
    
    No pre-processing
    Resampling: None 


## 모델 평가
### 훈련 모델의 예측 Class 측정(훈련데이터의 정확도)


```R
train_pred<-predict(FinalModel, train)
```


```R
# 오차행렬 적용
confusionMatrix(data = train_pred, reference = train$vote)
# 정확도 ->  Accuracy : 0.75             
```


    Confusion Matrix and Statistics
    
              Reference
    Prediction no yes
           no  19  13
           yes 24  92
                                              
                   Accuracy : 0.75            
                     95% CI : (0.6722, 0.8175)
        No Information Rate : 0.7095          
        P-Value [Acc > NIR] : 0.1597          
                                              
                      Kappa : 0.344           
                                              
     Mcnemar's Test P-Value : 0.1002          
                                              
                Sensitivity : 0.4419          
                Specificity : 0.8762          
             Pos Pred Value : 0.5937          
             Neg Pred Value : 0.7931          
                 Prevalence : 0.2905          
             Detection Rate : 0.1284          
       Detection Prevalence : 0.2162          
          Balanced Accuracy : 0.6590          
                                              
           'Positive' Class : no              
                                              



```R
confusionMatrix(data = train_pred, reference = train$vote, mode = "prec_recall")
# mode = "prec_recall" 설정하면 precision,recall 조회됨

```


    Confusion Matrix and Statistics
    
              Reference
    Prediction no yes
           no  19  13
           yes 24  92
                                              
                   Accuracy : 0.75            
                     95% CI : (0.6722, 0.8175)
        No Information Rate : 0.7095          
        P-Value [Acc > NIR] : 0.1597          
                                              
                      Kappa : 0.344           
                                              
     Mcnemar's Test P-Value : 0.1002          
                                              
                  Precision : 0.5938          
                     Recall : 0.4419          
                         F1 : 0.5067          
                 Prevalence : 0.2905          
             Detection Rate : 0.1284          
       Detection Prevalence : 0.2162          
          Balanced Accuracy : 0.6590          
                                              
           'Positive' Class : no              
                                              



```R
postResample(pred = train_pred, obs = train$vote)
```

```R

Accuracy

0.75

Kappa

0.344034499281265

```





### 테스트 모델의 예측 Class 측정



```R
test_pred<-predict(FinalModel, test)
```


```R
confusionMatrix(data = test_pred, reference = test$vote)

```


    Confusion Matrix and Statistics
    
              Reference
    Prediction no yes
           no   3   8
           yes 15  37
                                             
                   Accuracy : 0.6349         
                     95% CI : (0.504, 0.7527)
        No Information Rate : 0.7143         
        P-Value [Acc > NIR] : 0.9347         
                                             
                      Kappa : -0.0126        
                                             
     Mcnemar's Test P-Value : 0.2109         
                                             
                Sensitivity : 0.16667        
                Specificity : 0.82222        
             Pos Pred Value : 0.27273        
             Neg Pred Value : 0.71154        
                 Prevalence : 0.28571        
             Detection Rate : 0.04762        
       Detection Prevalence : 0.17460        
          Balanced Accuracy : 0.49444        
                                             
           'Positive' Class : no             
                                             



```R
confusionMatrix(data = test_pred, reference = test$vote, mode = "prec_recall")

```


    Confusion Matrix and Statistics
    
              Reference
    Prediction no yes
           no   3   8
           yes 15  37
                                             
                   Accuracy : 0.6349         
                     95% CI : (0.504, 0.7527)
        No Information Rate : 0.7143         
        P-Value [Acc > NIR] : 0.9347         
                                             
                      Kappa : -0.0126        
                                             
     Mcnemar's Test P-Value : 0.2109         
                                             
                  Precision : 0.27273        
                     Recall : 0.16667        
                         F1 : 0.20690        
                 Prevalence : 0.28571        
             Detection Rate : 0.04762        
       Detection Prevalence : 0.17460        
          Balanced Accuracy : 0.49444        
                                             
           'Positive' Class : no             
                                             



```R
postResample(pred = test_pred, obs = test$vote)
```

```R

Accuracy

0.634920634920635

Kappa

-0.0125786163522012

```





