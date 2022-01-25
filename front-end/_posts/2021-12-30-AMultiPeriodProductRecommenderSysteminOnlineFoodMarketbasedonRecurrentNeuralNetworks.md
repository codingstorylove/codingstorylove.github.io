---
layout: single
title: "A Multi-Period Product Recommender System in Online Food Market based on Recurrent Neural Networks 논문 공부 및 정리 (1)"
date: "2021-12-30 17:01:00 +0900"
last_modified_at: "2022-01-05 10:34:00 +0900"
---


# A Multi-Period Product Recommender System in Online Food Market based on Recurrent Neural Networks 논문 공부 및 정리 (1)

# 출처 :  Hea In Lee 1, Il Young Choi 2, Hyun Sil Moon 2 and Jae Kyeong Kim 3,*

# 출처 사이트 - [https://www.mdpi.com/2071-1050/12/3/969](https://www.mdpi.com/2071-1050/12/3/969)


1     Department of Social Network Science, Kyunghee University, Seoul 02447, Korea; hil106@khu.ac.kr

2     Graduate School of Business Administration & AI Research Management Center, Kyunghee University, Seoul 02447, Korea;

choice102@khu.ac.kr (I.Y.C.); pahunter@khu.ac.kr (H.S.M.)

3     School of Management, Kyunghee University, Seoul 02447, Korea; jaek@khu.ac.kr *  Correspondence: jaek @khu.ac.kr; Tel +82–2-
961–9355(J.K.K.)

Received: 13 December 2019; Accepted: 23 January 2020; Published: 29 January 2020




Abstract: A recommender system supports customers to find information, products, or services (such as music, books, movies, web sites, and digital contents), so it could help customers to make rapid routine decisions and save their time and money.

However, most existing recommender systems do not recommend items that are already purchased by the target customer, so are not suitable  for  considering  customers’  repetitive  purchase  behavior  or  purchasing order.

In  this research, we suggest a multi-period product recommender system, which can learn customers’ purchasing order and customers’ repetitive purchase pattern.

For such a purpose we applied the Recurrent  Neural  Network  (RNN),  which  is  one  of  the  artificial  neural  network  structures specialized  in  time  series  data  analysis, instead  of  collaborative  filtering  techniques.


Recommendation periods are segmented as various time-steps, and the proposed RNN-based recommender system can  recommend  items  by multiple periods  in  a time  sequence.

Several experiments with real online food market data show that the proposed system shows higher performance in accuracy and diversity in a multi-period perspective than the collaborative filtering- based system.

From the experimental results, we conclude that the proposed system is suitable for multi-period  product  recommendation,  which  results  in  robust  performance  considering well customers’ purchasing orders and customers’ repetitive purchase patterns.

Moreover, in terms of sustainability, we expect that our study contributes to the reduction of food wastes by inducing planned consumption, and the reduction of shopping time and effort.


개요: 추천 시스템은 고객이 정보, 제품 또는 서비스(예: 음악, 책, 영화, 웹 사이트 및 디지털 콘텐츠)를 찾도록 지원하므로 고객이 일상적인 결정을 빠르게 내리고 시간과 비용을 절약하는 데 도움이 될 수 있습니다.

그러나 기존의 대부분의 추천 시스템은 대상 고객이 이미 구매한 상품을 추천하지 않아 고객의 반복적인 구매 행동이나 구매 주문을 고려하기에 적합하지 않습니다.

본 연구에서는 고객의 구매 주문과 고객의 반복 구매 패턴을 학습할 수 있는 다중기간분석 모델 상품 추천 시스템을 제안합니다.

이를 위해 협업 필터링 기법 대신 시계열 데이터 분석에 특화된 인공신경망 구조 중 하나인 RNN(Recurrent Neural Network)을 적용했습니다.

추천 기간은 다양한 시간 단계로 구분되며 제안하는 RNN 기반 추천 시스템은 시간 순서대로 여러 기간으로 물품들을 추천할 수 있습니다.

실제 온라인 식품 시장 데이터에 대한 여러 실험은 제안된 시스템이 협업 필터링 기반 시스템보다 다중기간분석 모델 관점에서 정확도와 다양성에서 더 높은 성능을 보여줍니다.

실험 결과, 제안하는 시스템이 다중기간분석 모델 상품 추천에 적합하다는 결론을 내렸으며, 이는 고객의 구매 주문과 고객의 반복 구매 패턴을 고려하여 강력한 성능을 보입니다.

또한 지속가능성 측면에서 본 연구를 통해 계획된 소비를 유도하여 음식물쓰레기 감소, 쇼핑 시간 및 노력의 감소에 기여하기를 기대합니다.

1. Introduction

As the interest in personalization services increases in various fields, recommender systems applying various knowledge discovery techniques are being studied commercially and academically [1].

Especially, product recommendation systems, mostly developed based on online commerce, have been gradually becoming important in terms of sales and customer relationship as well as helping consumers to choose [2].

Collaborative filtering (CF) is a technique known as showing the best performance in product recommender systems [3,4].

The underlying assumption of collaborative filtering is that ‘customers with similar preferences for particular items will show similar preferences for other  items’.

CF-based  recommendation  models  predict  preference  based  on  the  similarity between users or items, but scalability and sparsity problems may have occurred due to data increases as e-commerce grows [4].

1. 소개

다양한 분야에서 개인화 서비스에 대한 관심이 높아짐에 따라 다양한 지식 발견 기법을 적용한 추천 시스템이 상업적, 학술적으로 연구되고 있습니다.[1].

특히, 주로 온라인 커머스를 기반으로 개발된 상품 추천 시스템은 소비자의 선택을 도울 뿐만 아니라 판매 및 고객 관계 측면에서 점차 중요해지고 있습니다[2].

CF(Collaborative Filtering)는 제품 추천 시스템에서 최고의 성능을 보이는 것으로 알려진 기술입니다[3,4].

협업 필터링의 기본 가정은 '특정 물품에 대해 유사한 선호도를 가진 고객이 다른 물품에 대해서도 유사한 선호도를 보일 것'이라는 것입니다.

CF 기반 추천 모델은 사용자 또는 물품 간의 유사도를 기반으로 선호도를 예측하지만 전자상거래가 성장함에 따라 데이터가 증가하여 확장성 및 희소성 문제가 발생할 수 있습니다.[4]



However, as online business and technology advances, customers transaction data not only increases [5] but also consumers’ consumption patterns are changing [6].

Consumers not only use online purchases to buy cellular phone accessories and clothes, but they also buy almost all products they need for everyday life.

The fresh food market is a typical example where preference  changes  over  time  and  recurring  purchases  frequently  occur.  Its  products  are  not expensive enough for frequent purchases so customers suffer repetitive decision-making problems.

Therefore, there is a need for recommender systems that can process and analyze a huge amount of data and recommend the multi-period products needed in everyday life.

Multi-period product recommender  systems  reflect  purchasing  patterns  of  customers,  but  traditional  CF-based recommender systems do not consider well customers’ purchasing orders [7].

그러나 온라인 비즈니스와 기술이 발전함에 따라 고객의 거래 데이터가 증가할 뿐만 아니라[5] 소비자의 소비 패턴도 변화하고 있습니다[6].

소비자는 온라인 구매를 통해 휴대폰 액세서리와 옷을 구매할 뿐만 아니라 일상 생활에 필요한 거의 모든 제품을 구매합니다.

신선식품 시장은 시간이 지남에 따라 선호도가 변하고 반복 구매가 빈번한 대표적인 예이다. 잦은 구매에 충분히 가격이 비싸지 않아 반복적인 의사결정에 어려움을 겪습니다.

따라서 방대한 양의 데이터를 처리 및 분석하여 일상생활에 필요한 다중기간분석 모델 상품을 추천할 수 있는 추천 시스템이 필요합니다.

다중기간분석 모델 상품 추천 시스템은 고객의 구매 패턴을 반영하지만, 기존의 CF 기반 추천 시스템은 고객의 구매 주문을 잘 고려하지 않습니다[7].

In recent years, interest in artificial neural networks has been increasing, and it has been used in various fields such as pattern recognition, natural language processing, and image recognition [8].

Among various structures of artificial neural networks, Recurrent Neural Networks (RNN) show outstanding performance on sequential data, because RNN has a structure that is able to selectively pass information across sequence steps while processing sequential data one element at a time [9,10].

최근 인공신경망에 대한 관심이 높아지고 있으며, 패턴인식, 자연어처리, 영상인식 등 다양한 분야에서 활용되고 있습니다.[8].

인공 신경망의 다양한 구조 중 RNN(Recurrent Neural Networks)은 순차 데이터에 대해 뛰어난 성능을 보입니다. RNN은 순차 데이터를 한 번에 한 요소씩 처리하면서 순차적인 단계에 정보를 선택적으로 전달할 수 있는 구조를 가지고 있기 때문입니다.[9,10]. .


# sequential data 이란?

# 참고사이트 - [https://m.post.naver.com/viewer/postView.naver?volumeNo=15510128&memberNo=36733075](https://m.post.naver.com/viewer/postView.naver?volumeNo=15510128&memberNo=36733075)

순차 데이터란 ‘데이터 집합 내의 객체들이 어떤 순서를 가진 데이터’로, 그 순서가 변경될 경우 고유의 특성을 잃어버리는 특징이 있습니다. 쉬운 예로, ‘도’와 ‘시’라는 글자 객체들로 ‘도시’와 ‘시도’라는 단어들의 조합이 가능하지만 이들은 서로 다른 의미를 갖는 것과 같습니다.   이처럼 우리가 사용하는 언어는 물론, IoT 기기에서 생성되는 센서 데이터, 주가 변동, 지진파, 그리고 DNA 염기서열 등 수없이 많은 종류의 시계열 데이터(time-series data)는 모두 순차 데이터에 포함됩니다.



In  this  study,  we  propose  a  multi-period  product  recommender  system  using  RNN  that considers customers’ purchase order.

It can reflect the customers’ preference changes and is expected to improve the recommendation quality compared with comparative existing recommender systems.

For the evaluation of the multi-period product recommender systems, we segment recommendation period  as  various  time-steps  and  the  proposed  system  and  other  benchmark  systems  are comparatively evaluated by multiple periods in a time sequence.

Also, the previously purchased products are also included from the recommendation list in this paper.

As in the case of frequent repetitive purchasing products such as fresh food, recommender systems should consider repurchase behavior.

It is important to consider items to be repurchased in advance because this can provide a positive effect to both customers and sellers, such as giving appropriate promotions, customer churn prevention, and increasing sales through them.

본 연구에서는 고객의 구매 주문을 고려한 RNN을 이용한 다중기간분석 모델 상품 추천 시스템을 제안합니다.

고객의 선호도 변화를 반영할 수 있으며, 기존의 추천 시스템에 비해 추천 품질이 향상될 것으로 기대됩니다.

다중기간분석 모델 상품 추천 시스템의 평가를 위해 추천 기간을 다양한 시간 단계로 분류하고 제안 시스템과 기준 시스템을 여러 기간으로 시간 순서대로 비교 평가합니다.

또한 기존에 구매한 제품들도 본 논문의 추천 목록에 포함되어 있습니다.

신선식품과 같이 빈번한 반복구매 상품의 경우와 마찬가지로 추천 시스템은 재구매 행동을 고려해야 합니다.

적절한 프로모션 제공, 고객 이탈 방지, 매출 증대 등 고객과 판매자 모두에게 긍정적인 효과를 줄 수 있으므로 재구매 물품을 미리 고려하는 것이 중요합니다.

The real online transaction data is used for experiments and we compare the recommendation performance  between  the  proposed  RNN-based  recommender  system  and  the  benchmark recommender system, item-based CF [11].


실제 온라인 거래 데이터를 실험에 사용하고 제안하는 RNN 기반 추천 시스템과 기준 추천 시스템인 아이템 기반 CF의 추천 성능을 비교합니다.[11].


# item-based CF 이란?

# 참고사이트 - [https://codingstorylove.github.io/front-end/ItemBasedCollaborativeFilteringInPython/](https://codingstorylove.github.io/front-end/ItemBasedCollaborativeFilteringInPython/)

Item-based collaborative filtering is the recommendation system to use the similarity between items using the ratings by users. In this article, I explain its basic concept and practice how to make the item-based collaborative filtering using Python.


아이템 기반 협업 필터링은 사용자의 평점을 이용하여 아이템 간의 유사도를 활용하는 추천 시스템입니다. 이 기사에서는 Python을 사용하여 항목 기반 협업 필터링을 만드는 방법의 기본 개념과 실습을 설명합니다.

# 다시 논문으로 돌아가서 설명하자면

Experiment results indicate that the proposed system outperforms higher recommendation accuracy and diversity in a multi-period perspective.

Based on the experiments, we claim that it is beneficial to consider purchase order information to recommend repurchasing items in multi-period recommender systems.

실험 결과 제안된 시스템은 다중 기간 관점에서 더 높은 추천 정확도와 다양성을 능가하는 것으로 나타났습니다.

실험을 바탕으로 다중기간분석 모델 추천 시스템에서 재구매 상품을 추천하기 위해 구매 주문 정보를 고려하는 것이 유익하다고 주장합니다.

# 2. Literature Review

2.1. Recommender Systems

Recommender systems, also called recommendation systems, are kind of information filtering systems that analyzes user’s past behavior data and seek to predict the user’s preference to items [12].

They are mainly focused on individuals who lack the personal experience, not a segmentation group of customers.

Since the 1990s, various recommender systems have been studied in various domains such as movies, music, books, articles, social media and products in general [4,13,14,15].

Burke [16] distinguishes recommendation approaches  to five different classes; content-based, collaborative filtering (CF), demographic, knowledge-based, and hybrid recommender systems.

Among them, CF is considered as a representative model of a recommender system, which was first introduced by Goldberg et al. [3].

Collaborative filtering builds a model to predict the preference based on the similarity  between  users  or  items,  and  the  underlying  assumption  is  that  users  with  similar preferences for a particular item will have similar preferences for other items.

Some studies have pointed that the main advantages of these neighborhood-based methods  compared  with other approaches are simplicity, justifiability, efficiency, and so on

# 2. 문헌 검토

2.1. 추천 시스템

추천 시스템은 사용자의 과거 행동 데이터를 분석하고 물품에 대한 사용자의 선호도를 예측하는 일종의 정보 필터링 시스템입니다[12].

그들은 세분화된 고객 그룹이 아니라 개인 경험이 부족한 개인에 주로 초점을 맞춥니다.

1990년대 이후 영화, 음악, 책, 기사, 소셜미디어, 제품 전반에 걸쳐 다양한 영역에서 다양한 추천 시스템이 연구되고 있습니다[4,13,14,15].

Burke[16]는 추천시스템 접근 방식을 5가지 다른 클래스로 구분합니다. 콘텐츠 기반, 협업 필터링(CF), 인구 통계, 지식 기반 및 하이브리드 추천 시스템.

그 중 CF는 Goldberg et al.에 의해 처음 소개된 추천 시스템의 대표적인 모델로 간주됩니다. [3].

협업 필터링은 사용자 또는 물품 간의 유사성을 기반으로 선호도를 예측하는 모델을 구축하며 기본 가정은 특정 물품에 대한 선호도가 유사한 사용자가 다른 물품에 대한 선호도도 유사하다는 것입니다.

일부 연구에서는 다른 접근 방식과 비교하여 이러한 이웃 기반 방법의 주요 이점이 단순성, 정당성, 효율성 등이라고 지적했습니다.

Therefore, many studies have been conducted to improve their performance using some supplementary tools based on machine learning techniques such as clustering.

However, collaborative filtering has often suffered from a problem of sparsity caused by a lack of data to predict users’ preferences for items [17,18].

The number of items sold on an e-commerce site is very large, but even the most active users would have rated only a small part of the entire items.

Therefore, even the most popular items have no rating data.

In addition, as online purchases have become popular recently, available item-user data sets have increased.

As a result, the computational complexity for recommendations has increased, resulting in scalability problems [4].

As well as sparsity and scalability problems due to the increase of usable data in recommender systems, most of the CF approaches have the problem that almost same products are recommended because it does not reflect the change of customers’ preference over time.

Most of the CF approaches only use information about user’s purchases over a specific period and do not utilize information  about  the  order  of  purchase  items.

Song  et  al.  [19]  attempted  to  find  changes  in customers’ behavior by creating association rules from two different points of view datasets and compared the association rules at two points to find out changes in customers’ purchase behavior over time.

However, they analyzed only a comparison between two points of purchasing using the association rules and did not find any long-term pattern changes.

따라서 클러스터링과 같은 기계 학습 기술을 기반으로 몇 가지 보완 도구를 사용하여 성능을 향상시키기 위한 많은 연구가 수행되었습니다.

그러나 협업 필터링은 물품에 대한 사용자의 선호도를 예측할 수 있는 데이터가 부족하여 희소성 문제가 자주 발생합니다[17,18].

전자 상거래 사이트에서 판매되는 물품의 수는 매우 많지만 가장 활동적인 사용자라도 전체 물품 중 일부만 평가했을 것입니다.

따라서 가장 인기 있는 물품에도 평점 데이터가 없습니다.

또한 최근 온라인 구매가 대중화되면서 사용 가능한 아이템-사용자 데이터 세트가 증가했습니다.

결과적으로 추천에 대한 계산 복잡성이 증가하여 확장성 문제가 발생했습니다[4].

추천 시스템에서 사용 가능한 데이터의 증가로 인한 희소성(sparse) 및 확장성(scalability) 문제뿐만 아니라, 대부분의 CF 접근 방식은 시간에 따른 고객의 선호도 변화를 반영하지 않아 거의 동일한 제품을 추천하는 문제가 있습니다.

대부분의 CF 접근 방식은 특정 기간 동안의 사용자 구매 정보만 사용하고 구매 물품의 주문 정보는 활용하지 않습니다.

Song et al. [19]는 서로 다른 두 가지 관점의 데이터 세트에서 연관 규칙을 생성하여 고객 행동의 변화를 찾고 두 지점에서 연관 규칙을 비교하여 시간 경과에 따른 고객의 구매 행동 변화를 찾으려고 했습니다.

그러나 그들은 연관 규칙을 사용하여 두 구매 지점 간의 비교만 분석했으며 장기적인 패턴 변화는 찾지 못했습니다.


# 클러스터링 이란?

# 참고사이트 - [https://kimdingko-world.tistory.com/107](https://kimdingko-world.tistory.com/107)

군집화(Clustering)이란 유사한 속성들을 갖는 데이터를 일정한 수의 군집으로 그룹핑하는 비지도 학습입니다.

머신러닝 모델(선형회귀, 다중선형회귀, 결정트리)는 특정 독립변수에 대한 종속변수(레이블, 정답)이 있는 경우입니다. 하지만 클러스터링의 경우는 정답이 없는 경우에 사용하며 속성별로 데이터를 나누게 됩니다. 때문에 몇개의 군집으로 나누어질지는 분석하는 입장에서 알 수 없다는 특징을 가집니다.

군집화 방법에는 분할적 군집화(Partitional Clustering), 계층적 군집화(Hierarchical Clustering), K-평균 군집화(K-Means Clustering) 가 존재합니다.

1. 분할적 군집화

특정 기준에 의해 동시에, 각 데이터들을 미리 정해진 개수의 군집 중 하나로 할당 시키는 군집화 방법입니다.

2. 분할적 군집화

특정 기준에 의해 동시에, 각 데이터들을 미리 정해진 개수의 군집 중 하나로 할당 시키는 군집화 방법입니다.

3. K-평균 군집화(K-Means Clustering)

K-평균 군집화란 대표적인 분할적 군집화 알고리즘으로 사전에 군집의 수(K)를 지정합니다.

각 군집은 하나의 중심점 (centroid, center)이 존재하고 각 데이터들을 가장 가까운 중심점에 해당하는 군집에 할당하는 방식입니다.



K-평균 군집화 알고리즘은 다음과 같습니다.

구하고자 하는 군집의 수(K) 설정

초기 데이터의 분포 상태에서 K개의 중심점을 임의로 지정

각 데이터들로부터 K개의 각 중심점 까지의 거리를 계산

각 데이터들을 가장 가까운 중심점이 속한 군집에 할당

K개의 중심점을 다시 계산하여 갱신 ( 중심점은 각 군집의 데이터들의 평균값)

중심점이 더 이상 변하지 않을 때까지 3, 4, 5 과정을 반복


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20211230_154435_1.png")
```




![output_10_0]({{ site.gdrive_url_prefix }}1bfFtuEBbp_9c-y8c3eOCFp5Rbs3BfaWB)





K(군집의 수)를 3으로 지정하고 임의의 점 c1, c2, c3를 지정합니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20211230_154435_2.png")
```




![output_12_0]({{ site.gdrive_url_prefix }}1VcPf5Nxe7aPWwdYLc1MRbwAYxp-SL_1N)





그 이후 모든 원소에 대해서 c1, c2, c3까지의 거리 중 가장 가까운 우너소에 군집을 할당합니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20211230_154435_3.png")
```




![output_14_0]({{ site.gdrive_url_prefix }}1ED-KoJYCuhze9rEPVm7Yy1NohvQSlMZc)




이 과정을 반복하고 나눠진 그룹 안에서 평균에 해당하는 점을 찾습니다.

모든 점들로부터의 거리(오차)의 합이 가장 작아지는 점을 찾고 모든 점에 대해 반복하면서 더이상 옮겨지지 않는 c1, c2, c3를 찾는 것이 최종 군집이 됩니다.

# 다시 논문으로 돌아가서 설명하자면

On the other hand, research has been proposed to reflect temporal dynamics in order to take into account the changing preferences of customers [20,21,22].

The method that reflects the temporal dynamics includes a way of reducing weight by time flow based on the time when the item is purchased or using a moving window method which only a certain period of data is used when recommending.

As  seen  in  previous  studies,  the  forgetting  method  was  used  to  capture  the preferences of customers.

This forgetting method is done either using a fixed-size moving window over data and repeatedly training the model with data in the window size using a decay function in the similarity calculation making older items less relevant [22,23].

In this study, we use RNN, an artificial neural network model suitable for sequential data, in which input and output are both sequences,  to  capture  preference  changes  in  individual  user-level.

We  propose  an  improved recommendation model that learns how previous purchase history affects the next purchase history.

반면에, 고객의 선호도 변화를 고려하기 위해 시간적 역학을 반영하는 연구가 제안되었습니다.[20,21,22].

시간적 역동성을 반영한 방법은 상품을 구매한 시점을 기준으로 시간의 흐름에 따라 가중치를 줄이는 방법과 추천 시 일정 기간의 데이터만 사용하는 무빙윈도우(moving window) 방법이 있습니다.

이전 연구에서 볼 수 있듯이 망각 방법은 고객의 선호도를 포착하는 데 사용되었습니다.

이 망각 방법은 데이터에 고정 크기 이동 창을 사용하고 유사성 계산에서 감쇠 함수를 사용하여 창 크기의 데이터로 모델을 반복적으로 훈련하여 오래된 물품의 관련성을 떨어뜨리는 방식으로 수행됩니다[22,23].


본 연구에서는 입력과 출력이 모두 시퀀스인 순차적 데이터에 적합한 인공 신경망 모델인 RNN을 사용하여 개별 사용자 수준의 선호도 변화를 포착합니다.

이전 구매 내역이 다음 구매 내역에 미치는 영향을 학습하는 개선된 추천 모델을 제안합니다.

2.2. RNN and Recommendation

RNN (Recurrent Neural Networks) is a structure appropriate for analyzing time series data since past information is stored in the hidden node and transferred to the next steps, which mean that previous input data can affect predictions of the current output [8].

The previous state is stored or memorized in the current state, that is, the data at time t can be considered when predicting the next state of time t+1.

The hidden unit of the RNN serves as a memory block, and each memory block receives data at a specific time t.

The memory block receiving the input at time t transfers the information to the connected memory block.

However, the input sequence gets longer, errors may not be conveyed forward, which may lead to a problem that is called long-term dependency.

In 1997, Hochreiter and Schmidhuber [24] first introduced the vanishing gradient problem and presented LSTM (Long Short-Term Memory) to address this problem.

In 2014, Cho et al. [25]. presented GRU (Gated Recurrent Unit), a simple variant of LSTM. Many variants of RNN have appeared, but LSTM and GRU are the most commonly used RNN structures.

2.2. RNN 및 추천

RNN(Recurrent Neural Networks)은 과거 정보가 은닉 노드에 저장되어 다음 단계로 전달되기 때문에 시계열 데이터 분석에 적합한 구조로, 이전 입력 데이터가 현재 출력 예측에 영향을 미칠 수 있음을 의미합니다.[8].

이전 상태는 현재 상태로 저장 또는 기억됩니다.즉, t+1 시점의 다음 상태를 예측할 때 t 시점의 데이터를 고려할 수 있습니다.

RNN의 히든 유닛은 메모리 블록 역할을 하며 각 메모리 블록은 특정 시간 t에 데이터를 수신합니다.

시간 t에서 입력을 받은 메모리 블록은 연결된 메모리 블록에 정보를 전달합니다.

그러나 입력 시퀀스가 길어지면 오류가 앞으로 전달되지 않아 장기 종속성이라는 문제가 발생할 수 있습니다.

1997년 Hochreiter와 Schmidhuber[24]는 소실 경사도 문제를 처음 소개하고 이 문제를 해결하기 위해 LSTM(Long Short-Term Memory)을 제시했습니다.

2014년, Cho et al. [25]. LSTM의 간단한 변형인 GRU(Gated Recurrent Unit)를 발표했습니다.

RNN의 많은 변형이 등장했지만 LSTM과 GRU가 가장 일반적으로 사용되는 RNN 구조입니다.

# LSTM 이란?

# 참고사이트 - [https://wegonnamakeit.tistory.com/7](https://wegonnamakeit.tistory.com/7)


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/논문/추천시스템 논문/data/20211230_160521_1.png")
```




![output_18_0]({{ site.gdrive_url_prefix }}1lC39UOQ2cZLW-mORt-ROEONc_6YYc9yP)





총 여섯 개의 파라미터가 있으며, 네 개의 게이트(gate)로 이루어져 있다.



1. Input (Xt)

2. (Cell) State

회전목마 같은 구조로 인해 오차가 사라지지 않고, 전체 체인을 관통함

(x) 게이트 메커니즘: 정보를 여닫는 역할

3. Hidden State

: 이전 출력(previous output)

4. Gates (Forget Gate, Input Gate, Output Gate)

세 개의 게이트는 정보들이 어느 시점에서 정보를 버리거나 유지하여 선택적으로 흘러갈 수 있게(=long term과 short term을 잘 고려하는) 하기 위함이다.

-  Forget Gate: 과거 정보를 버릴지 말지 결정하는 과정

과거의 정보를 통해 맥락을 고려하는 것도 중요하지만, 그 정보가 필요하지 않을 경우에는 과감히 버리는 것도 중요하다!

Decide what information we’re going to throw away from the cell state.

이전 output과 현재 input을 넣어, cell state로 가는 과거 정보값이 나옴

활성함수로 시그모이드(sigmoid)를 사용하므로, 0 또는 1 값이 나옴

> '0'일 경우, 이전의 cell state값은 모두 '0'이 되어 미래의 결과에 아무런 영향을 주지 않음


> '1'일 경우, 미래의 예측 결과에 영향을 주도록 이전의 cell state 값(Ct-1)을 그대로 보내 완전히 유지함
즉, Forget Gate는 현재 입력과 이전 출력을 고려해서,
cell state의 어떤 값을 버릴지/지워버릴지('0'이 출력되면 날려버림) 결정하는 역할

-  Input Gate: 현재 정보를 저장할지 결정하는 과정

Decide what new information we’re going to store in the cell state.

현재의 cell state값에 얼마나 더할지 말지를 정하는 역할 (tanh는 -1 에서 1 사이의 값이 나옴)

*** Forget Gate와 Input Gate의 주요 역할

: 이전 cell state 값을 얼마나 버릴지, 지금 입력과 이전 출력으로 얻어진 값을 얼마나 cell state에 반영할지 정하는 역할

- Output Gate (hidden state): 어떤 출력값을 출력할지 결정하는 과정

최종적으로 얻어진 cell state 값을 얼마나 빼낼지 결정하는 역할



# GRU 이란?

# 참고사이트-[https://wikidocs.net/22889](https://wikidocs.net/22889)

LSTM에서는 출력, 입력, 삭제 게이트라는 3개의 게이트가 존재했습니다. 반면, GRU에서는 업데이트 게이트와 리셋 게이트 두 가지 게이트만이 존재합니다. GRU는 LSTM보다 학습 속도가 빠르다고 알려져있지만 여러 평가에서 GRU는 LSTM과 비슷한 성능을 보인다고 알려져 있습니다

GRU와 LSTM 중 어떤 것이 모델의 성능면에서 더 낫다라고 단정지어 말할 수 없으며, 기존에 LSTM을 사용하면서 최적의 하이퍼파라미터를 찾아낸 상황이라면 굳이 GRU로 바꿔서 사용할 필요는 없습니다.

경험적으로 데이터 양이 적을 때는 매개 변수의 양이 적은 GRU가 조금 더 낫고, 데이터 양이 더 많으면 LSTM이 더 낫다고도 합니다. GRU보다 LSTM에 대한 연구나 사용량이 더 많은데, 이는 LSTM이 더 먼저 나온 구조이기 때문입니다.

# 다시 논문으로 돌아가서 설명하자면

Recently, studies using RNN have been actively carried out in research on recommender systems.

Zhang et al. [26] used RNN to predict online advertisement clicks and attempted to predict the user’s next clicks.

Hidasi et al. [27] viewed the recommendation problem as a session-based recommendation and then the use of Gated Recurrent Units (GRUs), a variant of RNN, for modeling user behavior in a session-based scenario in internet sites.

Based on these studies, the session-based recommendation, various follow-up studies have been conducted.

Tan et al. [28] presented various training methods to improve the performance of the model presented in Hidasi et al. [27]. I.e., data augmentation and a method to account for shifts in input data distribution.

However, session-based recommendation is only justified by the fact that most e-commerce sites do not track user behavior beyond the session level.

On the other hand, Song et al. [29] modeled both static and temporal user characteristics, assuming that user interest changes over time.

The authors saw static features as the entire dataset and temporal features as the most recent dataset.

Yu et al. [30] viewed the problem as the  next-basket  prediction  in  e-commerce.

최근에는 추천 시스템에 대한 연구에서 RNN을 이용한 연구가 활발히 진행되고 있습니다.

Zhang et al. [26]은 RNN을 사용하여 온라인 광고 클릭을 예측하고 사용자의 다음 클릭을 예측하려고 시도했습니다.

Hidasi et al. [27]은 권장 사항 문제를 세션 기반 권장 사항으로 보았고 인터넷 사이트의 세션 기반 시나리오에서 사용자 행동을 모델링하기 위해 RNN의 변형인 GRU(Gated Recurrent Units)를 사용했습니다.

이러한 연구들을 바탕으로 세션 기반 추천, 다양한 후속 연구가 진행되었습니다.

Tan et al. [28]은 Hidasi et al.에서 제시한 모델의 성능을 향상시키기 위한 다양한 훈련 방법을 제시했습니다. [27].

즉, 데이터 증가 및 입력 데이터 분포의 변화를 설명하는 방법입니다.

그러나 세션 기반 추천은 대부분의 전자 상거래 사이트가 세션 수준을 넘어서는 사용자 행동을 추적하지 않는다는 사실에 의해서만 정당화됩니다.

반면에, Song et al. [29]는 사용자의 관심이 시간이 지남에 따라 변한다고 가정하여 정적 및 시간적 사용자 특성을 모두 모델링했습니다.

저자는 정적 특성을 전체 데이터 세트로, 시간 특성을 가장 최근의 데이터 세트로 보았습니다.

Yu et al. [30]은 문제를 전자 상거래의 다음 장바구니 예측으로 보았습니다.

They  proposed  a RNN  model  with  real-valued representations of the baskets as an input and trained to rank the items in the next basket.

In those scenarios, items for a shopping cart are suggested based on a user’s history of past shopping carts.

As  previous  studies  have  shown,  attempts  have  been  made  to  improve  the  accuracy  of recommender systems using a RNN model for learning time-related or sequential information from continuous data.

In order to capture customers’ preference, we first divide the recommendation point into several sections to see whether customers’ preferences change or not.

In this study, the RNN and its variants will be reviewed and a RNN-based recommendation model considering purchase order is presented to compare the results from traditional recommendation models.

그들은 장바구니의 실제 값 표현을 입력으로 사용하는 RNN 모델을 제안하고 다음 장바구니의 물품 순위를 지정하도록 훈련했습니다.

이러한 시나리오에는 장바구니 물품은 사용자의 과거 장바구니 기록을 기반으로 제안됩니다.

이전 연구에서 알 수 있듯이 연속 데이터에서 시간 관련 또는 순차적 정보를 학습하기 위해 RNN 모델을 사용하여 추천 시스템의 정확도를 개선하려는 시도가 있었습니다.

고객의 선호도를 파악하기 위해 먼저 추천 포인트를 여러 섹션으로 나누어 고객의 선호도가 변하는지 여부를 확인합니다.

본 연구에서는 RNN과 그 변형을 검토하고 기존 추천 모델의 결과를 비교하기 위해 구매 주문을 고려한 RNN 기반 추천 모델을 제시합니다.


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

마찬가지로, 본 연구에서는 특정 시점 T-1 이전의 거래 정보를 분석하여 대상 고객이 다음 시점 T에서 아이템을 구매하려 권장되도록 가정합니다.

따라서 시점 T가 특정 시간범위를 의미하는 것은 아니고 장바구니 순서입니다.

예를 들어, 고객이 온라인 식품 시장을 통해 10번 구매하면 총 T는 10입니다.

T-1에서의 구매는 이전 구매(장바구니) 즉, 9번째 구매(장바구니)를 의미합니다.


# 두번째 논문 링크 -[https://codingstorylove.github.io/front-end/AMultiPeriodProductRecommenderSysteminOnlineFoodMarketbasedonRecurrentNeuralNetworks(2)/#page-title](https://codingstorylove.github.io/front-end/AMultiPeriodProductRecommenderSysteminOnlineFoodMarketbasedonRecurrentNeuralNetworks(2)/#page-title)
