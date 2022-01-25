---
layout: single
title: "A Multi-Period Product Recommender System in Online Food Market based on Recurrent Neural Networks 논문 공부 및 정리 (2)"
date: "2022-01-05 10:01:00 +0900"
last_modified_at: "2022-01-05 10:02:00 +0900"
---

# A Multi-Period Product Recommender System in Online Food Market based on Recurrent Neural Networks 논문 공부 및 정리 (2)

# 출처 :  Hea In Lee 1, Il Young Choi 2, Hyun Sil Moon 2 and Jae Kyeong Kim 3,*

# 출처 사이트 - [https://www.mdpi.com/2071-1050/12/3/969](https://www.mdpi.com/2071-1050/12/3/969)

1     Department of Social Network Science, Kyunghee University, Seoul 02447, Korea; hil106@khu.ac.kr

2     Graduate School of Business Administration & AI Research Management Center, Kyunghee University, Seoul 02447, Korea;

choice102@khu.ac.kr (I.Y.C.); pahunter@khu.ac.kr (H.S.M.)

3     School of Management, Kyunghee University, Seoul 02447, Korea; jaek@khu.ac.kr *  Correspondence: jaek @khu.ac.kr; Tel +82–2-
961–9355(J.K.K.)

Received: 13 December 2019; Accepted: 23 January 2020; Published: 29 January 2020




# 3. Methodology

3.1. Overview

The purpose of this study is to introduce a recommendation model considering purchase order to capture customers’ changing preferences and to examine the recommendation results of the proposed model from a multi-period perspective.

Generally, customers received recommendation items at time T based on the purchase history up to the time point T-1.

Likewise, in this study, it is assumed that the transaction information before the specific time point T-1 is analyzed and the target customer is recommended to purchase items at the next time point T.

Therefore, the time point T does not mean a specific timespan, but basket sequences.

For example, a customer purchases 10 times through  the  online  food  market,  total  T  equals  10.

The  purchase  at  T-1  means  the  previous purchase(basket), i.e., 9th purchase(basket).

# 3. 방법론

3.1. 개요

본 연구의 목적은 구매 주문을 고려한 추천 모델을 도입하여 고객의 변화하는 선호도를 파악하고 제안된 모델의 추천 결과를 다중기간분석 모델 관점에서 살펴보는 것입니다.

일반적으로 고객은 T-1 시점까지의 구매 내역을 기반으로 T 시점에 추천 상품을 받았습니다.

마찬가지로, 본 연구에서는 특정 시점 T-1 이전의 거래 정보를 분석하여 대상 고객이 다음 시점 T에서 아이템을 구매하도록 권장한다고 가정됩니다.

따라서 시점 T가 특정 시간범위를 의미하는 것은 아니고 장바구니 순서입니다.

예를 들어, 고객이 온라인 식품 시장을 통해 10번 구매하면 총 T는 10입니다.

T-1에서의 구매는 이전 구매(장바구니) 즉, 9번째 구매(장바구니)를 의미합니다.



```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20211230_174456_1.png")
```





![output_2_0]({{ site.gdrive_url_prefix }}1SFPzCuWGxreM1oJGJeQIdx49y0WgjtS9)


The recommendation model presents items likely to be purchased by customers at next time T+1 based on the previous purchasing history.

In this study, we propose a recommendation model considering  both  purchase  information  and  purchasing order  using  RNN  which  could  take sequential pattern into account.

The proposed RNN-based recommendation model is shown in Figure 2.

In the case of repetitive purchasing pattern over time, the RNN-based recommendation model could learn the temporal changes according to the purchasing order because RNN has a mechanism for storing the previous information in hidden units.

To design a RNN model for learning purchase information and purchasing order, data must be input to learn time-dependent features.

Since the input of the neural networks should be converted into a vector, each item iq is encoded as
a one-hot encoding and the purchase information Bt(Up)
of the customer Up is converted into multi-
hot encoding by adding one-hot encoding of items at the same purchasing order at time T.


추천 모델은 이전 구매 내역을 기반으로 다음 T+1 시점에 고객이 구매할 가능성이 있는 항목을 제시합니다.

본 연구에서는 순차적 패턴을 고려할 수 있는 RNN을 사용하여 구매 정보와 구매 주문을 모두 고려한 추천 모델을 제안합니다.

제안한 RNN 기반 추천 모델은 그림 2와 같습니다.

시간 경과에 따른 반복적인 구매 패턴의 경우 RNN은 숨겨진 유닛에서 이전 정보를 저장하는 메커니즘을 가지고 있기 때문에 RNN 기반 추천 모델은 구매 주문에 따른 시간적 변화를 학습할 수 있다.

구매 정보 및 구매 주문 학습을 위한 RNN 모델을 설계하려면 시간 종속 기능을 학습하기 위한 데이터를 입력해야 합니다.

신경망의 입력은 벡터로 변환되어야 하므로 각 물품 iq는 원핫 인코딩으로 인코딩되고 고객의 Up의 구매 정보 Bt(Up)는 T 시간에 동일한 구매 주문에서 물품의 원-핫 인코딩을 추가하여 다중 핫 인코딩으로 전환됩니다.



Xt is represented as a vector converted into a multi-hot encoding of all items in Bt(up) and the total length
of the vector is same as the number of all items.

iq is 1 if a customer purchased the item, otherwise it is 0.

The output Ot is passed through the softmax function and represented as the probability of purchase.

The model can be seen as learning the previous purchase information and representing the purchase pattern  that  will appear at the next time in probability.

In the learning process,  the difference between the predicted output (Ot) and the actual target (yT) is calculated by the loss function, here by category cross-entropy, and the weights are updated through back-propagation of the error. Finally, the top-N items with the greatest probability are recommended.

XT는 BT(up)의 모든 물품을 멀티-핫 인코딩으로 변환한 벡터로 표현됩니다.

그리고 벡터의 전체적인 길이는 모든 물품의 수와 같습니다.

iq는 고객이 품목을 구매한 경우 1이고 그렇지 않은 경우 0입니다.

출력 OT는 softmax 함수를 통해 전달되고 구매 확률로 표시됩니다.

모델은 이전 구매 정보를 학습하고 다음 번에 나타날 구매 패턴을 확률적으로 표현한 것으로 볼 수 있습니다.

학습 과정에서 예측된 출력(OT)과 실제 목표(yT) 간의 차이는 손실 함수(여기서는 범주 교차 엔트로피)에 의해 계산되고 가중치는 오류의 역전파를 통해 업데이트됩니다. 마지막으로 확률이 가장 높은 상위 N개 물품을 추천합니다.

# One-Hot Encoding 이란?

## 참고사이트 - [https://needjarvis.tistory.com/565](https://needjarvis.tistory.com/565)

n개의 범주형 데이터를 n개의 비트(0, 1) 벡터로 표현됩니다

서로 다른 범주 데이터는 독립적인 관계라는 것을 나타낼 수 있습니다

또한 keras으로 구현 가능합니다


# 다시 논문으로 돌아가서 설명하자면


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20211230_180549_1.png")
```






![output_5_0]({{ site.gdrive_url_prefix }}1PkeJcKOxq-6kXjaoWjeRhGJzpDewd92E)


3.3. Multi-period Recommender Systems

In this study, the proposed RNN-based recommendation model is evaluated by multiple periods observing  the  performance  of  various  recommendation  time-steps.

Traditional  recommender systems study only focus on the model accuracy of the next point in time when recommending items to the customer and evaluating model performance, which means they evaluate the accuracy of the model only once.

Indeed, customers’ preferences may change over time which may degrade the performance  of  the  recommendation  model.

So,  in  this  study,  recommendation  periods  are segmented as various time-steps, and the proposed RNN-based recommendation model is evaluated by multiple periods in a time sequence.

To be more precise, multi-period recommender systems evaluate the performance not only at time point T but also the subsequent time points such as T + 1, T + 2, and so on.

Figure 3 as below shows the example of a multi-period recommender system.

3.3. 다중기간분석모델 추천 시스템

이 연구에서 제안된 RNN 기반 추천 모델은 다양한 추천 시간 단계의 성능을 관찰하는 여러 기간에 의해 평가됩니다.

기존의 추천 시스템 연구는 고객에게 물품을 추천하고 모델 성능을 평가할 때 다음 시점의 모델 정확도에만 초점을 맞추므로 모델의 정확도를 한 번만 평가합니다.

실제로 고객의 선호도는 시간이 지남에 따라 변경되어 추천 모델의 성능을 저하시킬 수 있습니다.

따라서 본 연구에서는 추천 기간을 다양한 시간 단계로 구분하고 제안하는 RNN 기반 추천 모델을 시간 순서대로 여러 기간으로 평가합니다.

좀 더 정확하게 말하자면, 다중기간분석모델 추천 시스템은 T 시점뿐만 아니라 이후 T+1, T+2 등의 시점에서도 성능을 평가합니다.

아래 그림 3은 다중기간분석모델 추천 시스템의 예를 보여줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20211231_030403_1.png")
```





![output_7_0]({{ site.gdrive_url_prefix }}1kYe1vigyjDtwGsJ3tYz19n_i_JBhV-26)



3.4. Evaluation Metrics

Most of the recommender systems measure the accuracy of the recommended items because if the accuracy is not high, it means that the recommended items were not consumed by the users.

To measure  the  accuracy,  recall,  precision  and  F1  metrics  are  widely  used in  previous  studies [4,11,12,18,31].

In this paper, F1 measure is used to measure the accuracy of recommender systems because it considers both precision and recall to compute the score.

On the other hand, if the recommendation systems recommend similar items each time, there is a risk of reducing the diversity of the entire consumer, and if the similar items are recommended every time, the satisfaction of the recommendation systems will decrease.

Lathia et al. [32] mentioned that diversity should be pursued while maintaining a certain level of accuracy to increase satisfaction with the recommendation systems, and the following diversity metric is suggested.

3.4. 평가 지표

대부분의 추천 시스템은 추천 물품들의 정확도를 측정하는데 정확도가 높지 않다면 사용자가 추천 물품들을 소비하지 않았다는 뜻입니다.

정확도를 측정하기 위해 이전 연구에서 재현율, 정밀도 및 F1 측정항목이 널리 사용되었습니다[4,11,12,18,31].

본 논문에서는 추천 시스템의 정확도를 측정하기 위해 F1 측정을 사용하는데, 이는 점수를 계산할 때 정밀도와 재현율을 모두 고려하기 때문입니다.

반면 추천 시스템이 매번 유사한 물품들을 추천한다면 전체 소비자의 다양성을 감소시킬 위험이 있고, 매번 유사한 물품들을 추천한다면 추천 시스템의 만족도가 떨어질 것입니다.

Lathia et al. [32]는 추천 시스템에 대한 만족도를 높이려면 일정 수준의 정확도를 유지하면서 다양성을 추구해야 한다고 언급하고 다음과 같은 다양성 메트릭을 제안합니다.


# 재현율 ,정밀도 , F1 측정항목이란?

# 참고사이트 - [https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=sogangori&logNo=220986343741](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=sogangori&logNo=220986343741)

정확도 : 예측이 정답과 얼마나 정확한가?

정밀도 : 예측한 것중에 정답의 비율은?

재현율 : 찾아야 할 것중에 실제로 찾은 비율은?

F1 Score : 정밀도와 재현율의 평균

- 예시

번호 : [  1,    2,    3,    4,    5,    6  ]

정답 : [음치,음치,음치,음치,정상,정상]

예측 : [음치,음치,정상,정상,정상,정상]



정확도 : 예측이 맞은 비율은?

         1,2,5,6 번 맞추고 3,4번은 틀렸다. 6명중 4명 맞췄으므로 4/6 = 2/3 = 0.66

정밀도 : 음치라고 예측한 사람들 중에 진짜 음치가 얼마나 있는가?

          내가 음치라고 예측한 1,2번 이 둘다 음치가 맞았다. 2/2 = 1.00

재현율 : 전체 음치 중에서 내가 맞춘 음치의 비율은?

          원래 음치가 4명 있는데 나는 그중에서 2명을 맞췄다. 2/4 = 0.5

F1 Score : 정밀도와 재현율의 평균

            2 * 정밀도 * 재현율 /(정밀도+재현율) = 2 * 1.00 * 0.5 / (1.00 + 0.5) = 0.66



만약 음치 아니라 실력자를 찾는 것이 목적이었다면



정확도 : 예측이 맞은 비율은?

         1,2,5,6 번 맞추고 3,4번은 틀렸다. 6명중 4명 맞췄으므로 4/6 = 2/3 = 0.66

정밀도 : 실력자라고 예측한 사람들 중에 진짜 실력자가 얼마나 있는가?

          내가 실력자라고 예측한 3,4,5,6번 중 실력자는 5,6번 뿐이다. 2/4 = 0.5

재현율 : 전체 실력자 중에서 내가 맞춘 실력자의 비율은?

          원래 실력자가 2명 있는데 나는 그중에서 2명을 맞췄다.  2/2 = 1.00

F1 Score : 정밀도와 재현율의 평균

            2 * 정밀도 * 재현율 /(정밀도+재현율) = 2 * 0.5 * 1.0 / (0.5 + 1.0) = 0.66



대부분의  분류문제는 희소한 샘플을 찾는 것이 목적입니다.

위의 예에서 음치는 4명이고 정상이 2명입니다. 음치가 많고 정상이 희소하므로  아마도 정상을 찾는 것이 목표일 것입니다.





```python
import numpy as np

import sklearn.metrics as metrics



y = np.array([1,1,1,1,0,0]) #0은 정상(실력자), 1은 음치

p = np.array([1,1,0,0,0,0]) #나의 예측



accuracy = np.mean(np.equal(y,p))

right = np.sum(y * p == 1)

precision = right / np.sum(p)

recall = right / np.sum(y)

f1 = 2 * precision*recall/(precision+recall)



print('accuracy',accuracy)

print('precision', precision)

print('recall', recall)

print('f1', f1)


print("_______________________________")
# sklearn 을 이용하면 전부 계산해준다.

print("음치를 찾는게 목적일때")

print('accuracy', metrics.accuracy_score(y,p) )

print('precision', metrics.precision_score(y,p) )

print('recall', metrics.recall_score(y,p) )

print('f1', metrics.f1_score(y,p) )

print("_______________________________")

print("실력자를 찾는게 목적일때")
print('accuracy', metrics.accuracy_score(p,y) )

print('precision', metrics.precision_score(p,y) )

print('recall', metrics.recall_score(p,y) )

print('f1', metrics.f1_score(p,y) )
```

    accuracy 0.6666666666666666
    precision 1.0
    recall 0.5
    f1 0.6666666666666666
    _______________________________
    음치를 찾는게 목적일때
    accuracy 0.6666666666666666
    precision 1.0
    recall 0.5
    f1 0.6666666666666666
    _______________________________
    실력자를 찾는게 목적일때
    accuracy 0.6666666666666666
    precision 0.5
    recall 1.0
    f1 0.6666666666666666



```python

print(metrics.classification_report(y,p))

print(metrics.confusion_matrix(y,p))
```

                  precision    recall  f1-score   support

               0       0.50      1.00      0.67         2
               1       1.00      0.50      0.67         4

        accuracy                           0.67         6
       macro avg       0.75      0.75      0.67         6
    weighted avg       0.83      0.67      0.67         6

    [[2 0]
     [2 2]]


# 다시 논문으로 돌아가서 설명하자면


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20211231_031639_1.png")
```





![output_13_0]({{ site.gdrive_url_prefix }}1imBYNS2r05DBIpYGL8eW7D8GpmnDhl_x)



L1 and L2 are the recommended list and N is the number of recommended items. In this study, this temporal diversity metric is also used to measure the diversity of the recommended list.

L1과 L2는 추천 목록이고 N은 추천 물품들의 수입니다. 본 연구에서는 시간적 다양성 메트릭을 사용하여 추천 목록의 다양성을 측정하기도 합니다.

# 4. Evaluation

4.1. Data Description

The data used in this study is transaction data from Fresh Food Delivery Service Company in USA, published in 2017 at the data analysis competition platform Kaggle.

As mentioned above, its products’ prices are generally low and customers habitually purchase.

Therefore, it is a good data set for our experiment because our methodology considers repurchase behavior that other recommender systems are not interested in.

Moreover, the transactional data was collected for one year, so we could ignore seasonal factors that could affect model building and performance.

The data provides real purchase information for each customer and the order number which is indicated by an index assigned  to  each  customer  according  to  the  order  of  purchase  of  items.

For  experiments,  the purchased items by customers are arranged according to the order of purchase time, and finally, all the buying information corresponding to the same order number is composed of the same shopping list.

# 4. 평가

4.1. 데이터 설명

본 연구에 사용된 데이터는 2017년 데이터 분석 대회 플랫폼 Kaggle에 게재된 미국 Fresh Food Delivery Service Company의 거래 데이터를 사용하였습니다.

위에서 언급했듯이 제품의 가격이 일반적으로 저렴하고 고객이 습관적으로 구매합니다.

따라서 우리의 방법론은 다른 추천 시스템이 관심을 갖지 않는 재구매 행동을 고려하기 때문에 실험에 좋은 데이터 세트입니다.

또한 거래 데이터는 1년 동안 수집되었으므로 모델 구축 및 성능에 영향을 미칠 수 있는 계절적 요인을 무시할 수 있었습니다.

데이터는 각 고객에 대한 실제 구매 정보와 물품들의 구매 순서에 따라 각 고객에게 할당된 인덱스로 표시되는 주문 번호를 제공합니다.

실험을 위해 고객이 구매한 물품들을 구매 시간 순서대로 배열하고 마지막으로 동일한 주문 번호에 해당하는 모든 구매 정보가 동일한 쇼핑 목록으로 구성됩니다.


In order to compare the recommended performance of the models over time, we used 7716 customers’ shopping information with 10 shopping carts.

Generally, recommender systems based on these transaction data could not infer with the preference of extremely popular items because they are products almost everyone buys.

On the other hand, because sales sub-products are usually purchased by customers with unusual tastes, they could be outliers to build a model.

For these reasons, a total of 9073 items were used, except for items that appeared too often or appeared less frequently, so we excluded the top and lower 10% of the sales volume for the experiments.

As mentioned earlier, in this study, a recommendation model is measured by multiple recommendation periods.

For this purpose, at the recommendation time point T, all the information before point T is regarded as training data and the subsequent buying information is considered as test data.

시간 경과에 따른 모델의 추천 성능을 비교하기 위해 7716명의 고객 쇼핑 정보와 10개의 장바구니를 사용했습니다.

일반적으로 이러한 거래 데이터를 기반으로 하는 추천 시스템은 거의 모든 사람이 구매하는 제품이기 때문에 매우 인기 있는 물품들의 선호도를 추론할 수 없습니다.

반면, 판매 하위 제품은 일반적으로 특이한 취향을 가진 고객이 구매하기 때문에 모델을 구축하는 데 이상치가 될 수 있습니다.

이러한 이유로 너무 자주 등장하거나 덜 자주 등장하는 물품들을 제외하고 총 9073개의 물품들이 사용되었으므로 실험에서는 판매량의 상위 10%와 하위 10%를 제외했습니다.

앞서 언급한 바와 같이 본 연구에서는 추천 모델을 여러 추천 기간으로 측정합니다.

이를 위해 추천 시점 T에서 시점 T 이전의 모든 정보는 학습 데이터로 간주하고 후속 구매 정보는 테스트 데이터로 간주합니다.

4.2. Experimental Setup

As with most studies on neural networks, we have also experimented various recurrent neural networks structure (basic RNN, LSTM, GRU) and parameters to be used as a recommendation model.

First, we experiment with the number of hidden nodes in the basic RNN, LSTM, and GRU.

As the number of hidden nodes increases, the initial learning rapidly progresses and quickly converges.

However, since the number of parameters to learn increases as the number of hidden nodes increases, the optimal number of hidden nodes is set to 100.

Also, since LSTM is slightly better than basic RNN and GRU, LSTM is used as a final model of recommendation in this study.

Since the increase in the number of layers does not contribute to the improvement of our suggested model performance, so the number of hidden layers is set to one in our LSTM structure.

4.2. 실험 설정

신경망에 대한 대부분의 연구와 마찬가지로 우리는 다양한 순환 신경망 구조(기본 RNN, LSTM, GRU)와 추천 모델로 사용할 매개변수도 실험했습니다.

먼저 기본 RNN, LSTM 및 GRU에서 숨겨진 노드의 수를 실험합니다.

은닉 노드의 수가 증가할수록 초기 학습이 빠르게 진행되고 빠르게 수렴됩니다.

그러나 은닉 노드의 수가 증가할수록 학습할 파라미터의 수가 증가하므로 최적의 은닉 노드의 수는 100으로 설정합니다.

또한 LSTM이 기본 RNN 및 GRU보다 약간 우수하므로 LSTM을 최종 모델로 사용합니다.

레이어 수의 증가는 제안된 모델 성능 향상에 기여하지 않으므로 LSTM 구조에서 은닉층 수를 하나로 설정합니다.

# 은닉층 이란?

# 참고사이트 - [https://deepai.org/machine-learning-glossary-and-terms/hidden-layer-machine-learning](https://deepai.org/machine-learning-glossary-and-terms/hidden-layer-machine-learning)

In neural networks, a hidden layer is located between the input and output of the algorithm, in which the function applies weights to the inputs and directs them through an activation function as the output.

In short, the hidden layers perform nonlinear transformations of the inputs entered into the network.

Hidden layers vary depending on the function of the neural network, and similarly, the layers may vary depending on their associated weights.

How does a Hidden Layer work?

Hidden layers, simply put, are layers of mathematical functions each designed to produce an output specific to an intended result.

For example, some forms of hidden layers are known as squashing functions.

These functions are particularly useful when the intended output of the algorithm is a probability because they take an input and produce an output value between 0 and 1, the range for defining probability.

신경망에서 은닉층은 알고리즘의 입력과 출력 사이에 위치하며, 여기서 은닉층의 기능은 입력에 가중치를 적용하고 출력으로 활성화 함수를 통해 지시합니다.

간단히 말해서, 은닉층은 네트워크에 입력된 입력의 비선형 변환을 수행합니다.

은닉층은 신경망의 기능에 따라 달라지며, 마찬가지로 연관된 가중치에 따라 달라질 수 있습니다.

은닉층은 어떻게 작동합니까?

은닉층은 간단히 말해서 의도한 결과에 특정한 출력을 생성하도록 설계된 수학 함수의 층입니다.

예를 들어, 은닉층의 일부 형태는 스쿼싱 함수으로 알려져 있습니다.

이러한 함수는 입력을 받아 확률을 정의하는 범위인 0과 1 사이의 출력 값을 생성하기 때문에 알고리즘의 의도된 출력이 확률인 경우 특히 유용합니다.

Hidden layers allow for the function of a neural network to be broken down into specific transformations of the data.

Each hidden layer function is specialized to produce a defined output.

For example, a hidden layer functions that are used to identify human eyes and ears may be used in conjunction by subsequent layers to identify faces in images.

While the functions to identify eyes alone are not enough to independently recognize objects, they can function jointly within a neural network.


Hidden Layers and Machine Learning

Hidden layers are very common in neural networks, however their use and architecture often varies from case to case.

As referenced above, hidden layers can be separated by their functional characteristics. For example, in a CNN used for object recognition, a hidden layer that is used to identify wheels cannot solely identify a car, however when placed in conjunction with additional layers used to identify windows, a large metallic body, and headlights, the neural network can then make predictions and identify possible cars within visual data.

은닉층을 사용하면 신경망의 기능을 데이터의 특정 변환으로 나눌 수 있습니다.

각 은닉층 기능은 정의된 출력을 생성하도록 특화되어 있습니다.

예를 들어, 사람의 눈과 귀를 식별하는 데 사용되는 은닉층 기능은 이미지에서 얼굴을 식별하기 위해 후속 층과 함께 사용될 수 있습니다.

눈을 식별하는 기능만으로는 객체를 독립적으로 인식하기에 충분하지 않지만, 신경망 내에서는 공동으로 역할을 할 수 있습니다.

은닉층과 머신 러닝

은닉층은 신경망에서 매우 일반적이지만, 사용 및 구조는 경우에 따라 다릅니다.

위에서 언급한 바와 같이 은닉층은 기능적 특징에 따라 분리될 수 있습니다.


# 다시 논문으로 돌아가서 설명하자면



Also the optimization function should be determined by experiment, as it is known that there is no optimization function that ﬁts into all problems so it should be set on an experimental basis.

Figure 4 shows the experimental results of various optimization algorithms.

In this ﬁgure, when an entire dataset was passed through the neural network model, an epoch was complete.

For example, 10 epochs mean that an entire dataset was passed through the model 10 times.

Therefore, as the epoch increases, the loss value decreases.

Category cross-entropy was used as a loss function.

Among optimization algorithms, Adam optimizer which shows the best performance is selected, and the hyper-parameter is set by the value known as the best default value.

또한 최적화 함수는 모든 문제에 맞는 최적화 함수가 없기 때문에 실험을 해서 결정과 설정해야 합니다.

그림 4는 다양한 최적화 알고리즘의 실험 결과를 보여줍니다.

이 그림에서 전체 데이터 세트가 신경망 모델을 통과했을 때 epoch이 완료되었습니다.

예를 들어, 10 Epoch는 전체 데이터 세트가 모델을 10번 통과했음을 의미합니다.

따라서 Epoch가 증가할수록 손실 값은 감소합니다.

범주 교차 엔트로피는 손실 함수로 사용되었습니다.

최적화 알고리즘 중 가장 성능이 좋은 Adam 최적화를 선택하고, 하이퍼파라미터는 최적의 기본값으로 알려진 값으로 설정합니다.


# 파라미터(Parameter)와 하이퍼 파라미터(Hyper parameter) 차이는?

# 참고사이트 - [https://bkshin.tistory.com/entry/%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-13-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0Parameter%EC%99%80-%ED%95%98%EC%9D%B4%ED%8D%BC-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0Hyper-parameter](https://bkshin.tistory.com/entry/%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-13-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0Parameter%EC%99%80-%ED%95%98%EC%9D%B4%ED%8D%BC-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0Hyper-parameter)



파라미터는 한국어로 매개변수입니다. 파라미터는 모델 내부에서 결정되는 변수입니다. 또한 그 값은 데이터로부터 결정됩니다. 무슨 말인지 예를 들어 설명해보겠습니다. 한 클래스에 속해 있는 학생들의 키에 대한 정규분포를 그린다고 합시다. 정규분포를 그리면 평균(μ)과 표준편차(σ) 값이 구해집니다. 여기서 평균과 표준편차는 파라미터(parameter)입니다. 파라미터는 데이터를 통해 구해지며 (They are estimated or learned from data), 모델 내부적으로 결정되는 값입니다. 사용자에 의해 조정되지 않습니다. (They are often not set manually by the practitioner)

선형 회귀의 계수도 마찬가지입니다. 수많은 데이터가 있고, 그 데이터에 대해 선형 회귀를 했을 때 계수가 결정됩니다. 이 계수는 사용자가 직접 설정하는 것이 아니라 모델링에 의해 자동으로 결정되는 값입니다. (They are required by the model when making predictions)

하이퍼 파라미터는 모델링할 때 사용자가 직접 세팅해주는 값을 뜻합니다. (They are often specified by the practitioner) learning rate나 서포트 벡터 머신에서의 C, sigma 값, KNN에서의 K값 등등 굉장히 많습니다. 머신러닝 모델을 쓸 때 사용자가 직접 세팅해야 하는 값은 상당히 많습니다. 그 모든 게 다 하이퍼 파라미터입니다. 하지만, 많은 사람들이 그런 값들을 조정할 때 그냥 '모델의 파라미터를 조정한다'라는 표현을 씁니다. 원칙적으로는 '모델의 하이퍼 파라미터를 조정한다'라고 해야 합니다.

하이퍼 파라미터는 정해진 최적의 값이 없습니다. 휴리스틱한 방법이나 경험 법칙(rules of thumb)에 의해 결정하는 경우가 많습니다. (They can often be set using heuristics) 베이지안 옵티미제이션과 같이 자동으로 하이퍼 파라미터를 선택해주는 라이브러리도 있긴 합니다.

파라미터와 하이퍼 파라미터를 구분하는 기준은 사용자가 직접 설정하느냐 아니냐입니다. 사용자가 직접 설정하면 하이퍼 파라미터, 모델 혹은 데이터에 의해 결정되면 파라미터입니다.

# 다시 논문으로 돌아가서 설명하자면



- 최적화 알고리즘 성능 비교




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20220103_044906_1.png")
```





![output_24_0]({{ site.gdrive_url_prefix }}1cC4dfaNkTzD4zIKgGLpsh-8ezJn4myf9)




4.3.  Experimental Results

The proposed LSTM-based recommendation model and the comparison recommendation model are examined using accuracy metrics when the top ﬁve items are recommended in the multi-period perspective.

First, we analyze the models by dividing time from various recommendation periods and examining whether the accuracy changes as periods change over time.

The comparison model is an item-based CF and popularity model.

Item-based CF (represented by CF) is a similarity-based recommendation model that recommends items which are similar to the items purchased by the target user.

Popularity model (represented by POP) is a recommendation model that recommends the best-selling items, which is simple but widely applied in many circumstances.

4.3. 실험 결과

제안된 LSTM 기반 추천 모델과 비교 추천 모델은 다기간 관점에서 상위 5개 물품을 추천할 때 정확도 측정항목을 사용하여 검사됩니다.

먼저, 다양한 추천 기간으로부터 시간을 나누어 모델을 분석하고, 시간이 지남에 따라 기간의 변화에 정확도가 변화하는지 살펴보았습니다.

비교모델은 아이템 기반 CF와 인기모델입니다.

아이템 기반 CF(CF로 표시)는 타겟 사용자가 구매한 아이템과 유사한 아이템을 추천하는 유사도 기반 추천 모델입니다.

인기모델(POP로 표시)은 베스트셀러 아이템을 추천하는 추천모델로 단순하지만 많은 상황에서 널리 적용됩니다.


Experiment I: In this experiment, the model is trained with ﬁve shopping items for all customers, which are contained from the ﬁrst to ﬁfth shopping lists.

Then the recommendation model is used to predict what customers will purchase in multi-period perspective.

The recommendation model is trained at time T with the purchasing history up to time T-1.

So, the recommended item list after time T, which is denoted by T+1, T+2 and so on, is also predicted using the model trained at time T.

Since each recommendation model, which is trained until time T, is examined from the multi-period perspective, and it is denoted as MP (multi-period) in this study.

Figure 5 shows the experimental method and the result of recommendation accuracy measured from a multi-period perspective using a F1 score when recommending the top ﬁve items.

실험 I: 이 실험에서 모델은 첫 번째부터 다섯 번째 쇼핑 목록까지 포함된 모든 고객에 대해 5개의 쇼핑 물품으로 학습됩니다.

그런 다음 추천 모델을 사용하여 고객이 다기간 관점에서 무엇을 구매할지 예측합니다.

추천 모델은 T-1까지의 구매 내역으로 T 시간에 학습됩니다.

따라서 T+1, T+2 등으로 표시되는 T 시간 이후의 추천 아이템 리스트 또한 T 시간에 학습된 모델을 이용하여 예측합니다.

T 시간까지 학습된 각 추천 모델을 다기적 관점에서 검사하기 때문에, 본 연구에서는 이를 MP(multi-period)로 표기합니다.

그림 5는 상위 5개 물품을 추천할 때 F1 점수를 사용하여 다기간 관점에서 추천 정확도를 측정한 실험 방법과 결과를 보여주고 있습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20220104_132802_1.png")
```




![output_27_0]({{ site.gdrive_url_prefix }}17l_Q87QW_VbGFlrmbimHZm3cTwwU26pp)




The experimental results evaluated by multiple periods from T to T+4 in a time sequence show that the accuracy of the proposed model and the comparison model decreases over time.

Overall, the accuracy of the popularity model is as low as about 1%.

The popularity model is like a mass marketing strategy which recommends the best-selling items and not a model that provides a personalized recommendation.

This result shows that the personalized recommendation model can more satisfy consumer satisfaction.

As shown in Figure 5, the F1 score of the proposed LSTM-based recommendation model is higher than that of the CF-based model, and is about 21% higher at T and about 10% higher at T+4.

These results show that the LSTM-based recommendation model considering purchase order not only recommends items more accurately than CF-based model, but also predicts more accurately from the multi-period perspective.

T부터 T+4까지 여러 주기로 시간 순서대로 평가한 실험 결과는 제안 모델과 비교 모델의 정확도가 시간이 지남에 따라 감소함을 보였습니다.

전반적으로, 인기도 모델의 정확도는 약 1% 정도로 낮습니다.

인기 모델은 개인화된 추천 모델이 아니라 베스트 셀러 상품을 추천하는 거대한 마케팅 전략과 같습니다.

이 결과는 개인화 추천 모델이 소비자 만족도를 더 만족시킬 수 있음을 보여줍니다.

그림 5에서 보는 바와 같이 제안된 LSTM 기반 추천 모델의 F1 점수는 CF 기반 모델보다 높으며 T에서 약 21%, T+4에서 약 10% 높습니다.

이러한 결과는 구매 주문을 고려한 LSTM 기반 추천 모델이 CF 기반 모델보다 더 정확하게 물품을 추천할 뿐만 아니라 다기간 관점에서 더 정확하게 예측함을 보여줍니다.

Experiment II: In experiment I, the model was trained until time T, and the recommendation results were examined from the multi-period perspective.

Experiment II is conducted assuming that the actual purchase information at time T+1, T+2 and so on for all customers is known.

In other words, the recommendation model is trained again as the time changes.

In these experiments, a moving window method is used which means that the latest ﬁve shopping lists are used to train the recommendation model, denoted as MOV (MOVing window) in this study.

Figure 6 shows the MOV method (a) and the resulting F1 score (b) when the model recommends top ﬁve items.

실험 II: 실험 I에서, T 시간까지 모델을 학습시켰고, 다기간 관점에서 추천 결과를 검사하였다.

실험 II는 모든 고객에 대한 T+1, T+2 시점 등의 실제 구매 정보를 알고 있다고 가정하고 수행합니다.

즉, 추천 모델은 시간이 지남에 따라 다시 학습됩니다.

이 실험에서는 최근 5개의 쇼핑 목록을 사용하여 추천 모델을 학습시키는 moving window 방법을 사용하며 본 연구에서는 MOV(MOVing 창)로 표시합니다.

그림 6은 모델이 상위 5개 항목을 추천할 때 MOV 방법(a)과 결과 F1 점수(b)를 보여줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20220104_133809_1.png")
```





![output_30_0]({{ site.gdrive_url_prefix }}1Uh1Pc_DzDcxQsZUS_v-FgJTB9bbxH-X1)


As can be expected, when the recommendation model learns the actual purchase information, it shows higher accuracy than the recommendation model ﬁxed at T point.

The popularity model also shows about 1% accuracy, but with slightly more accuracy using the moving window method.

The F1 score of the proposed LSTM-based recommendation model with the MOV method is higher than that of the MP method by about 8% at T+1, 19% at T+2, 29% at T+3 and 33% at T+4.

On the other hand, the CF-based recommendation model with the MOV method is higher than the MP method at about 7% at T+1, 13% at T+2, 18% at T+3 and 25% at T+4.

These results show that the proposed LSTM-based recommendation model predicts more accurately what customer prefers than the CF-based model.

Also, this result implies that customers’ preference is changing over time, because the result of moving window is more accurate than that of multi-period method.

예상대로 추천 모델이 실제 구매 정보를 학습할 때, T point에 고정된 추천 모델보다 높은 정확도를 보입니다.

인기 모델도 약 1%의 정확도를 보여주지만 moving window 방법을 사용하면 정확도가 약간 더 높습니다.

MOV 방법을 적용한 제안된 LSTM 기반 추천 모델의 F1 점수는 MP 방법보다 T+1에서 약 8%, T+2에서 19%, T+3에서 29%, T+4에서 33% 높게 나타났습니다.

반면, MOV 방법의 CF 기반 추천 모델은 T+1에서 약 7%, T+2에서 13%, T+3에서 18%, T+4에서 25%로 MP 방법보다 높습니다.

이러한 결과는 제안된 LSTM 기반 추천 모델이 CF 기반 모델보다 고객이 선호하는 것을 더 정확하게 예측한다는 것을 보여줍니다.

또한 이 결과는 moving window 결과가 다기간 방법보다 더 정확하기 때문에, 시간이 지남에 따라 고객의 선호도가 변하고 있음을 시사합니다.

Experiment III: In experiment I and II, the training data is set to only ﬁve items.

To identify whether there is data recency eﬀect, experiment III is conducted, where all previous transaction data at recommending time T are used as training data.

We denote this cumulative method as CUM and MOV of LSTM model and CF model.

Experimental results show that the overall F1 score of LSTM-based model is higher than that of the CF-based model.

But the graph in Figure 7b shows a diﬀerent pattern between the LSTM-based model and CF-based model.

In the CF-based recommendation model, the moving window method, which learns data of speciﬁc window size only, is more accurate than the cumulative method.

However, there is no signiﬁcant diﬀerence between the moving window method and the cumulative method in the proposed LSTM-based model.

It implies that the LSTM-based model has little eﬀect on data recency compared to the CF-based model.

The reason is that the LSTM model could consider long and short-term memory in the process of training process using customers’ purchase order.

실험 III: 실험 I 및 II에서, 훈련 데이터는 5개 물품으로만 설정됩니다.

데이터 최근 효과가 있는지 없는지 확인하기 위해서 추천 시간 T의 이전 거래 데이터를 모두 훈련 데이터로 사용하는 실험 III를 수행합니다.

이 누적 방법을 LSTM 모델과 CF 모델의 MOV와 CUM으로 표시합니다.

실험 결과 LSTM 기반 모델의 전체 F1 점수가 CF 기반 모델보다 높은 것으로 나타났습니다.

그러나 그림 7b의 그래프는 LSTM 기반 모델과 CF 기반 모델 간에 다른 패턴을 보여줍니다.

CF 기반 추천 모델에서, 특정 윈도우 크기의 데이터만 학습하는 Moving window 방법이 누적 방법보다 더 정확합니다.

그러나 제안된 LSTM 기반 모델에서는 Moving window 방법과 누적 방법의 차이가 크지 않습니다.

이는 LSTM 기반 모델이 CF 기반 모델에 비해 데이터 최신성에 거의 영향을 미치지 않는다는 것을 의미합니다.

그 이유는 LSTM 모델이 고객의 구매 주문을 이용한 학습 과정에서 장단기 기억을 고려할 수 있기 때문입니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20220104_140954_1.png")
```



![output_33_0]({{ site.gdrive_url_prefix }}17ZtniCjp4F08E9rTtv9mDwSyI2gTdOgc)




Table 1 shows the diversity results of the LSTM-based recommendation model and the CF-based recommendation model.

The diversity measure used in this experiment has a value ranging from 0 to 1. If the score is closer to one, it means that the result of the recommendation model is more diverse between the previous recommended item list and the subsequent recommended items list.

Table 1은 LSTM 기반 추천 모델과 CF 기반 추천 모델의 다양성 결과를 보여줍니다.

본 실험에서 사용한 다양성 척도는 0부터 1까지의 값을 가지며, 1에 가까울수록 이전 추천 물품 목록과 후속 추천 물품 목록 사이에 추천 모델의 결과가 더 다양하다는 것을 의미합니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20220104_141722_1.png")
```




![output_35_0]({{ site.gdrive_url_prefix }}17qdM7gja1BlxyHcllUiSCgrqBwOKm721)





The experimental result shows that the CF-based model recommends almost similar items between the previous and subsequent recommend time, while the proposed LSTM-based model recommends more diverse items.

Although the CF-based model recommends more diverse items when using the moving window method compared to the cumulative method, the LSTM-based model has no signiﬁcant diﬀerence between the moving window method and the cumulative method.

However, since the moving window method can utilize transaction data more eﬃciently, so it is thought to be a more eﬀective learning method than the cumulative method, if there is not a signiﬁcant diﬀerence in recommendation accuracy.

Furthermore, the diversity of the proposed model is relatively higher even in the condition that items purchased previously are also recommended.

This result implies that the LSTM-based model not only considers well customers’ purchasing orders but also captures well customers’ purchasing patterns so it could suggest more diverse items.

실험 결과 제안된 LSTM 기반 모델은 더 다양한 물품을 추천하는 반면, CF 기반 모델은 이전과 이후 추천 시간 사이에 거의 유사한 물품을 추천하는 것으로 나타납니다.

CF 기반 모델은 누적 방법에 비해 moving window 방법을 사용할 때 더 다양한 물품을 추천하지만 LSTM 기반 모델은 moving window 방법과 누적 방법 간에 큰 차이가 없습니다.

그러나 moving window 방법은 거래 데이터를 보다 더 효율적으로 활용할 수 있기 때문에, 추천 정확도에서 큰 차이가 없다면 누적 방법보다 더 효과적인 학습 방법이라고 생각됩니다.

게다가 이전에 구매한 물품도 추천하는 조건에서 제안 모델의 다양성이 상대적으로 높습니다.

이러한 결과는 LSTM 기반 모델이 고객의 구매 주문을 잘 고려할 뿐만 아니라 고객의 구매 패턴을 잘 포착하여 더 다양한 물품을 제안할 수 있음을 시사합니다.

Experiment IV: The results of the previous experiments show that when knowing the actual purchasing information, it is helpful to recommend more accurate items.

But in the real world, when recommending items at time T+1, T+2 and so on, the actual purchasing information is unknown like the multi-period method.

Also, as shown above, in the multi-period method, the results of the recommendation model at time T is also used at a subsequent recommendation time (T+1, T+2 and so on), which leads to degrading of the recommendation quality over time in a long-term perspective.

Therefore, we propose another method of predicting and recommending items that are expected to be purchased from a long-term perspective when the customer is going to buy the recommended items of the model.

We assume that the customer purchased the top ﬁve items recommended by the model at the previous time.

실험 IV: 이전 실험의 결과는 실제 구매 정보를 알 때, 보다 정확한 상품을 추천하는 데 도움이 되는 것으로 나타났습니다.

그러나 현실 세계에서는 T+1, T+2 등의 시점에 상품을 추천할 때, 다기간 방법과 같이 실제 구매 정보를 알 수 없습니다.

또한, 위에서 보듯이, 다기간 방법에서 시간 T에서의 추천 모델의 결과를 다음 추천 시간(T+1, T+2 등)에도 사용하기 때문에 추천의 품질이 장기간 관점에서 시간에 지남에 따라 저하됩니다.

따라서 고객이 모델의 추천 물품을 구매할 때, 장기적 관점에서 구매할 것으로 예상되는 물품을 예측하고 추천하는 또 다른 방법을 제안합니다.

고객이 이전에 모델이 추천한 상위 5개 물품을 구매했다고 가정합시다.



Then, the top ﬁve items are used as training data to retrain the model at the next time.

For the comparison with the multi-period method, this method which uses both all the previous transaction data and the model’s top ﬁve recommended items as training data is called the cumulative multi-period method, which is denoted as CUMMP in this study.

On the other hand, the moving window multi-period method, which is denoted as MOVMP, excludes the oldest transaction data as training data.

MOVMP uses both only the latest transaction data and the model’s top ﬁve recommended items as training data.

As shown in Figure 8, the actual purchase information after the time T is unknown as the multi-period method.

However, in the CUMMP and MOVMP method, the unknown data are replaced with the model’s recommended top ﬁve items.

Figure 9 shows the comparison results of the proposed LSTM-based recommendation model and the CF-based recommendation model, speciﬁcally the F1 score, among the MP, CUMMP and MOVMP method.



그런 다음, 상위 5개 물품은 다음 번에 모델을 다시 학습시키기 위한 학습 데이터로 사용됩니다.

다기간 방법과의 비교를 위해서 이전 거래 데이터와 모델의 상위 5개 추천 물품을 모두 훈련 데이터로 사용하는 이 방법을 누적 다기간 방법이라고 하며, 본 연구에서는 이를 CUMMP라고 합니다.

반면 MOVMP라고 하는 moving window 다기간 방법은 가장 오래된 거래 데이터를 훈련 데이터로 제외합니다.

MOVMP는 최신 거래 데이터와 모델의 상위 5개 추천 물품만 훈련 데이터으로 사용합니다.

그림 8에서 시간 T 이후의 실제 구매 정보는 다중 기간 방법으로 알려지지 않았습니다.

그러나 CUMMP 및 MOVMP 방법에서, 알 수 없는 데이터를 모델이 추천하는 상위 5개 물품으로 대체합니다.

그림 9는 MP, CUMMP, MOVMP 방법 중 제안된 LSTM 기반 추천 모델과 CF 기반 추천 모델, 특히 F1 점수의 비교 결과를 보여줍니다.




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20220104_143004_1.png")
```



![output_38_0]({{ site.gdrive_url_prefix }}1qRx9rXCcxf2oL-KZhiqKTPr-YJYO3COt)




As can be expected, both the proposed LSTM-based recommendation model and CF-based model show that in terms of long-term prediction recommendation accuracy decreases with time.

The LSTM-based recommendation model has about a 28%, 28%, 29% accuracy decline in MP, CUMMP and MOVMP methods respectively.

Meanwhile, the CF-based recommendation model has about a 21%, 24%, 24% accuracy decline in MP, CUMMP and MOVMP methods respectively.

The LSTM-based model shows a slightly larger decline, but the recommendation accuracy is still high.

Also, there is no signiﬁcant diﬀerence among MP, CUMMP and MOVMP methods in the proposed LSTM-based model, but the CF-based model shows that the accuracy of the CUMMP and MOVMP methods is found to be further decreased than the MP method.

예상할 수 있듯이 제안된 LSTM 기반 추천 모델과 CF 기반 모델 모두 장기적 예측 측면에서 추천 정확도가 시간이 지남에 따라 감소함을 보여줍니다.

LSTM 기반 추천 모델은 MP, CUMMP 및 MOVMP 방법에서 각각 약 28%, 28%, 29% 정확도 감소를 보입니다.

한편, CF 기반 추천 모델은 MP, CUMMP 및 MOVMP 방법에서 각각 약 21%, 24%, 24% 정확도 감소를 보입니다.

LSTM 기반 모델은 약간 더 큰 감소를 보이지만 추천 정확도는 여전히 높습니다.

또한 제안하는 LSTM 기반 모델에서는 MP, CUMMP, MOVMP 방법의 큰 차이가 없으나 CF 기반 모델은 MP 방법보다 CUMMP와 MOVMP 방법의 정확도가 더 떨어지는 것을 알 수 있습니다.


Since the CF-based model using CUMMP or MOVMP methods uses the model’s recommended items (together with purchased items) as training data, it can be interpreted that the CUMMP or MOVMP methods do not reﬂect the changing preferences of customers compared to MP.

On the other hand, while the accuracy of the LSTM-based recommendation model is high even in the CUMMP or MOVMP methods compared to CF, however, it is found that there is no signiﬁcant diﬀerence compared to the MP method.

From experiment IV, we conclude that the LSTM-based recommendation model gives robust results compared to CF-based model, because
the  LSTM-based  model  gives  almost  the  same  result  with  purchase  data  only  and  purchase  data  with predicted data.


CUMMP 또는 MOVMP 방법을 사용하는 CF 기반 모델은 모델의 추천 물품(구매 항목과 함께)을 학습 데이터로 사용하므로 CUMMP 또는 MOVMP 방법은 MP에 비해 고객의 선호도 변화를 반영하지 않는 것으로 해석할 수 있습니다.

한편, CUMMP나 MOVMP 방법에서도 LSTM 기반 추천 모델의 정확도는 CF에 비해 높지만 MP 방법과 비교해서 큰 차이가 없음을 알 수 있습니다.

실험 IV에서 우리는 LSTM 기반 추천 모델이 CF 기반 모델에 비해 강력한 결과를 제공한다는 결론을 내렸습니다.
LSTM 기반 모델은 구매 데이터만 있는 경우와 예측된 데이터가 있는 구매 데이터의 경우에 거의 동일한 결과를 제공하기 때문입니다.





```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20220104_145116_1.png")
```


![output_40_0]({{ site.gdrive_url_prefix }}1mQyzDptGU-uNRwS9jK1q1xGtgN7ITt5c)





# 5.  Conclusions

In this study, Recurrent Neural Network, which is specialized in time series data analysis, is applied to recommendation model.

The proposed LSTM-based recommendation model is comparatively evaluated with the following two models;

item-based collaborative ﬁltering model, which is widely used in the recommender systems research as a benchmark system, and the popularity model, which is relatively simple but still used in the business ﬁeld.

Recommendation periods are segmented as various time-steps, and the proposed LSTM-based recommendation model is evaluated by multiple periods in a time sequence.

Real online transaction data of a fresh food delivery market is used as a data set and the recommendation model’s performance is evaluated by accuracy and diversity from a multi-period perspective.

# 5. 결론

본 연구에서는 시계열 데이터 분석에 특화된 Recurrent Neural Network를 추천 모델에 적용하였습니다.

제안된 LSTM 기반 추천 모델은 다음 두 모델로 비교 평가됩니다.

추천 시스템 연구에서 벤치마크 시스템으로 널리 사용되는 아이템 기반 협업 필터링 모델과 비교적 간단하지만 여전히 비즈니스 분야에서 사용되는 인기 모델이 있습니다.

추천 기간은 다양한 시간 단계로 구분되며, 제안된 LSTM 기반 추천 모델은 시간 순서대로 여러 기간으로 평가됩니다.

신선식품 배달 시장의 실제 온라인 거래 데이터를 데이터 세트로 사용하고 다기간 관점에서 정확성과 다양성으로 추천 모델의 성능을 평가합니다.

The experimental results are as follows. First, the LSTM-based recommendation model outperformed the CF-based model in a multi-period perspective.

Precisely, the proposed LSTM-based recommendation model is about 21% higher at T and about 10% higher at T+4.

This result shows that considering the purchase order not only helps to improve the recommendation quality but also gives a more accurate prediction in multi periods.

In addition, the proposed LSTM-based recommendation model recommends more diverse items than the CF-based model.

It implies that the proposed model captures the customers’ purchase patterns well and oﬀers various items to customers.

Also, experimental results show that the proposed model has no signiﬁcant diﬀerence in model accuracy regardless of the size of the training data, which represents the robustness of the proposed model.

However, even if there is no signiﬁcant diﬀerence in recommendation accuracy and diversity, it is better to use the data eﬃciently through the moving window method, considering the cost of learning the LSTM-based recommendation model.

Finally, in the perspective of long-term predictions, both the LSTM-based model and the CF-based model results in decreasing recommendation accuracy over time, but the accuracy curve of the LSTM-based model is of more gradual descent than that the CF-based model.

실험 결과는 다음과 같다. 첫째, LSTM 기반 추천 모델은 다기간 관점에서 CF 기반 모델보다 우수한 성능을 보였습니다.

정확히 말하면, 제안하는 LSTM 기반 추천 모델은 T에서 약 21%, T+4에서 약 10% 더 높습니다.

이 결과는 구매 주문을 고려하는 것이 추천 품질을 향상시키는 데 도움이 될 뿐만 아니라 여러 기간에서 더 정확한 예측을 제공함을 보여줍니다.

또한 제안된 LSTM 기반 추천 모델은 CF 기반 모델보다 더 다양한 물품들을 추천합니다.

이는 제안된 모델이 고객의 구매 패턴을 잘 포착하여 고객에게 다양한 물품들을 제공함을 의미합니다.

또한 실험 결과에서 제안 모델은 훈련 데이터의 크기에 관계없이 모델 정확도에 큰 차이가 없는 것으로 나타나 제안 모델의 견고함을 나타냅니다.

그러나 추천 정확도와 다양성에 큰 차이가 없더라도 LSTM 기반 추천 모델을 학습하는 비용을 고려하면 moving window 방법을 통해 데이터를 효율적으로 사용하는 것이 좋습니다.

마지막으로, 장기 예측의 관점에서, LSTM 기반 모델과 CF 기반 모델 모두 시간이 지남에 따라 추천 정확도가 감소하지만 LSTM 기반 모델의 정확도 곡선은 CF 기반 모델보다 점진적으로 하강합니다.

This   study   extended   the   recommendation   periods   as   various   time-steps   and   evaluated   the performance by multiple periods in a time sequence.


Unlike previous recommender systems researches, which focus on recommendation accuracy at the single point of view, we compare the accuracy of the recommendation model with multiple periods and show that the proposed model has a better performance even at a multi-period perspective.

In applying the suggested recommendation methodology in the real market, it will be necessary to set the update frequency of the recommended model.

In the actual stage of operation, the model is suggested to be updated daily or weekly to reﬂect the changing preferences of users to increase the accuracy and diversity of recommendation results.

But the exact update frequency is to be decided by several experiments with a real data set, and may be diﬀerent from the characteristic of items, number of items, number of customers, and the average time between two sequenced purchases.

본 연구에서는 추천 기간을 다양한 시간 단계로 확장하고 시간 순서대로 여러 기간으로 성능을 평가했습니다.

단일 관점에서 추천 정확도에 초점을 맞춘 기존의 추천 시스템 연구와 달리,  추천 모델의 정확도를 다중 기간과 비교하여 제안 모델이 다중 기간 관점에서도 더 나은 성능을 가진다는 것을 보여줍니다.

제안된 추천 방법론을 실제 시장에 적용하기 위해서는 추천 모델의 업데이트 빈도를 설정하는 것이 필요할 것이다.

실제 운영 단계에서는 사용자의 선호도 변화를 반영하여 모델을 매일 또는 매주 업데이트하여 추천 결과의 정확성과 다양성을 높일 것을 제안합니다.

그러나 정확한 업데이트 빈도는 실제 데이터 세트로 여러 번의 실험을 통해 결정해야 하며, 물품들의 특성, 물품들의 수, 고객들의 수, 연속 구매 2건의 평균 시간에 따라 다를 수 있습니다.


In terms of sustainability, highly accurate multi-point recommendations that reﬂect changing customer preferences can help market managers prevent products with a very short shelf life, such as fresh vegetables, from being discarded.

Furthermore, as our suggested multi-point recommender system is a kind of decision support system, it could help customers to make rapid routine decisions and save their time and money.

Moreover, we expect that our study contributes to the customers’ reduction of food wastes by inducing planned consumption.

In this study, online-based retail transaction data are used, but applying the LSTM-based recommendation model to more diverse transaction data set is a promising future research area.

In addition, it will be also a promising research topic to combine the CF-based model with the proposed RNN-based recommendation model.

지속 가능성 측면에서 변화하는 고객 선호도를 반영하는 매우 정확한 다중점 추천은 시장 관리자가 유통 기한이 매우 짧은 신선한 야채와 같은 제품들이 폐기되는 것을 방지하는 데 도움이 될 수 있습니다.

또한 제안하는 다중점 추천 시스템은 일종의 의사 결정 지원 시스템이므로 고객이 일상적인 의사 결정을 빠르게 내리고 시간과 비용을 절약할 수 있게 도와줄 수 있습니다.

또한 계획된 소비를 유도하여 고객의 음식물쓰레기 감소에 기여하기를 기대합니다.

본 연구에서는 온라인 기반의 소매 거래 데이터를 사용하지만 LSTM 기반 추천 모델을 보다 다양한 거래 데이터 세트에 적용하는 것은 향후 유망한 연구 분야입니다.

또한 CF 기반 모델과 제안된 RNN 기반 추천 모델을 결합하는 것도 유망한 연구 주제가 될 것입니다.

