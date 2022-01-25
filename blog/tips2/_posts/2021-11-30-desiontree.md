---
layout: single
title: "의사결정 나무(Decision Tree)"
date: "2021-11-29 12:18:00 +0900"
last_modified_at: "2021-11-29 12:18:00 +0900"
---


# 의사결정 나무(Decision Tree)

의사결정 나무(Decision Tree) 알고리즘을 설명하기 전에 Tree의 기본적인 지식을 정리해보자.

최상위의 노드를 Root node(루트 노드)라고 하고, 자식이 없는 노드를 Terminal node (단말 노드)라고 한다.

아래의 트리를 보면 트리의 깊이가 3임을 알 수 있다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/분류 알고리즘/data/20211129_210413_1.png")
```






![output_2_0]({{ site.gdrive_url_prefix }}1yEUOrdcFuCi0j9hOS7w4mrmHFfpRrs1B)

# 불순도 (Impurity)

- 분기기준 설정 시 현재노드의 불순도에 비해 자식노드의 불순도가 감소되도록 설정해야하며

- 현재 노드의 불순도와 자식노드의 불순도 차이를 Information Gain(정보 획득)이라고 합니다.

불순도(Impurity)란 해당 범주 안에 서로 다른 데이터가 얼마나 섞여 있는지를 뜻합니다. 아래 그림에서 위쪽 범주는 불순도가 낮고, 아래쪽 범주는 불순도가 높습니다. 바꾸어 말하면 위쪽 범주는 순도(Purity)가 높고, 아래쪽 범주는 순도가 낮습니다. 위쪽 범주는 다 빨간점인데 하나만 파란점이므로 불순도가 낮다고 할 수 있습니다. 반면 아래쪽 범주는 5개는 파란점, 3개는 빨간점으로 서로 다른 데이터가 많이 섞여 있어 불순도가 높습니다.



```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/분류 알고리즘/data/20211129_235840_1.png")
```






![output_4_0]({{ site.gdrive_url_prefix }}1orbZIKDHQJGSTXfWf23FhvjpOq9VrpCR)

- 출처 : https://bkshin.tistory.com/entry/%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-4-%EA%B2%B0%EC%A0%95-%ED%8A%B8%EB%A6%ACDecision-Tree

## 불순도 함수 (Gini, Entropy)

불순도를 수치적으로 나타낼 수 있는 대표적인 불순도 함수는 두가지가 있습니다.




```python
Image("C://Users/MyCom/jupyter-tutorial/분류 알고리즘/data/20211129_231448_1.png")
```






![output_7_0]({{ site.gdrive_url_prefix }}1w5RkAAQocAZEnV625D5ghX3iF-D4obBg)


엔트로피(Entropy)는 불순도(Impurity)를 수치적으로 나타낸 척도입니다. 엔트로피가 높다는 것은 불순도가 높다는 뜻이고, 엔트로피가 낮다는 것은 불순도가 낮다는 뜻입니다. 엔트로피가 1이면 불순도가 최대입니다. 즉, 한 범주 안에 서로 다른 데이터가 정확히 반반 있다는 뜻입니다. 엔트로피가 0이면 불순도는 최소입니다. 한 범주 안에 하나의 데이터만 있다는 뜻입니다.


```python
Image("C://Users/MyCom/jupyter-tutorial/분류 알고리즘/data/20211129_235023_1.png")
```





![output_9_0]({{ site.gdrive_url_prefix }}1F8y5qhZW1syNhTcrL8IVBO-q4e6eLyK6)

# 정보획득 (Information gain)
분기 이전의 불순도와 분기 이후의 불순도의 차이를 정보 획득이라고 합니다.

불순도가 1인 상태에서 0.7인 상태로 바뀌었다면 정보 획득(information gain)은 0.3입니다.

자세한 결정트리 구성 단계는 다음과 같습니다.

1. Root 노드의 불순도 계산

2. 나머지 속성에 대해 분할 후 자식노드의 불순도 계산

3. 각 속성에 대한 Information Gain 계산 후 Information Gain(Root노드와 자식노드의 불순도 차이)이 최대가 되는 분기조건을 찾아 분기

4. 모든 leaf 노드의 불순도가 0이 될때까지 2,3을 반복 수행한다.

- 출처 :https://wooono.tistory.com/104
