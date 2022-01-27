---
layout: single
title: "how to use process discovery algorithms in Python"
date: "2021-12-01 12:18:00 +0900"
last_modified_at: "2021-12-01 12:18:00 +0900"
---

# Learn the basics of process mining and how to use process discovery algorithms in Python

- 참고사이트 : [https://towardsdatascience.com/introduction-to-process-mining-5f4ce985b7e5](https://towardsdatascience.com/introduction-to-process-mining-5f4ce985b7e5)

In current times, the majority of apps/websites we are using on a daily basis are greatly interested in how we are using them.

That is because they want to learn from the users’ behavior and improve in order to attract or retain more users.

현재 우리가 일상적으로 사용하는 대부분의 앱/웹사이트는 우리가 사용하는 방식에 큰 관심을 가지고 있습니다.

더 많은 사용자를 유치하거나 유지하기 위해 사용자의 행동에서 배우고 개선하기를 원하기 때문입니다.

Imagine your favorite e-commerce app. Most likely, you are free to browse the products that the company is offering, and every now and then you are gently nudged to create an account in order to actually purchase something.

In order to actually become a registered user, you often need to provide some information about you (the amount of information greatly varies per service).

After you go through a few screens, you are ready to go and purchase.

좋아하는 전자 상거래 앱을 상상해보십시오. 대부분의 경우 회사에서 제공하는 제품을 자유롭게 탐색할 수 있으며 때때로 실제로 무언가를 구매하기 위해 계정을 만들도록 부드럽게 움직입니다.

실제로 등록된 사용자가 되려면 귀하에 대한 몇 가지 정보를 제공해야 하는 경우가 많습니다(정보의 양은 서비스마다 크게 다름).

몇 개의 화면을 통과한 후에는 구매할 준비가 된 것입니다.

Disclaimer: The description above is a simplification and many e-commerce retailers follow different ways of making the users sign-up, for example, requiring sign-up at the last step before paying for the order or actually kindly offering to save your details after ordering for your convenience — so you can order faster next time.

What happens in the background is the work of data scientists/analysts who analyze the user journey and potentially create some kind of funnel analysis — the mapping and analysis of a series of events that lead towards a defined goal, like completing the sign-up or making a purchase.

Funnel analysis is a great and intuitive tool for identifying the dropoff at each of the stages and calculating the conversion rates. Below you can see an example of a dashboard presenting the funnel.

면책 조항: 위의 설명은 단순화되었으며 많은 전자 상거래 소매업체는 사용자 가입을 만드는 다양한 방법을 따릅니다.  다음에 더 빨리 주문할 수 있도록 귀하의 편의를 위한 주문후에 상세사항을 저장한다.

백그라운드에서 발생하는 일은 사용자 여정을 분석하고 잠재적으로 일종의 깔때기 분석을 생성하는 데이터 과학자/분석가의 작업입니다. 예를 들어 가입 완료 또는 구매하기.

깔때기 분석은 각 단계에서 하락을 식별하고 전환율을 계산하기 위한 훌륭하고 직관적인 도구입니다.

We can use the funnel analysis to identify which step of the user journey results in the highest dropoff and then spend some extra resources on investigating why and potentially improving it in order to retain more users.

In a simple world, funnels would be enough to understand user behavior. However, life is not that simple and many apps/websites offer complex and non-linear experience. That is why we can supplement the funnel analysis with a set of additional analyses, one of them being process mining.

In this article, I will present a brief overview to the topic of process mining and demonstrate the most popular process discovery algorithms. I will also recommend additional resources for a more in-depth read.

깔때기 분석을 사용하여 사용자 여정의 어느 단계에서 가장 많이 이탈했는지 식별한 다음 더 많은 사용자를 유지하기 위해 이유를 조사하고 잠재적으로 개선하는 데 약간의 추가 자원을 사용할 수 있습니다.

단순한 세상에서 깔때기는 사용자 행동을 이해하기에 충분할 것입니다. 그러나 삶은 그렇게 단순하지 않으며 많은 앱/웹사이트는 복잡하고 비선형적인 경험을 제공합니다. 이것이 우리가 일련의 추가 분석으로 깔때기 분석을 보완할 수 있는 이유이며 그 중 하나는 프로세스 마이닝입니다.

이 기사에서는 프로세스 마이닝 주제에 대한 간략한 개요를 제시하고 가장 널리 사용되는 프로세스 검색 알고리즘을 보여줍니다. 나는 또한 더 깊이 읽을 수 있는 추가 자료를 추천할 것입니다.

# Process discovery algorithms in Python

In this part, we will go over a few of the most popular process mining algorithms and apply them to a toy dataset in Python.

For that, we will use the pm4py library (process mining for Python, in case you wondered).

It is also worth mentioning that for many researchers/practitioners the leading process mining software was ProM (free software) or Fluxicon’s Disco (commercial software). There are also R packages, for example processmapR within the bupaR ecosystem.

# Python의 프로세스 검색 알고리즘

이 부분에서는 가장 인기 있는 프로세스 마이닝 알고리즘을 살펴보고 이를 Python의 데이터 세트에 적용합니다.

이를 위해 pm4py 라이브러리(궁금한 경우를 대비하여 Python용 프로세스 마이닝)를 사용합니다.

또한 많은 연구원/실무자에게 주요 프로세스 마이닝 소프트웨어는 ProM(자유 소프트웨어) 또는 Fluxicon의 Disco(상업 소프트웨어)였다는 점을 언급할 가치가 있습니다. bupaR 생태계 내에 processmapR과 같은 R 패키지도 있습니다.

Data preparation — event logs

Process mining is based on event logs. We can describe event logs as a sequence of sequences of events. In most cases, whenever the process is supported by any IT system, it produces some kind of a log of actions completed by the users.

For example, the log can contain all the actions a user performed in the app.

In order to carry out process discovery, the dataset must contain the following 3 types of information:

Case ID — a unique identifier of an entity going through the process. A common example could be the user’s unique ID, although, many possibilities are valid (it depends on the use case).

Event — a step of the process, any activity that is a part of the process we are analyzing.

Timestamp — used for performance evaluation and determining the order of events, can be the time when the user entered/exited the given event (or both actually).

데이터 준비 — 이벤트 로그

프로세스 마이닝은 이벤트 로그를 기반으로 합니다. 이벤트 로그를 이벤트 시퀀스의 시퀀스로 설명할 수 있습니다. 대부분의 경우 프로세스가 IT 시스템에서 지원될 때마다 사용자가 완료한 일종의 작업 로그가 생성됩니다.

예를 들어, 로그에는 사용자가 앱에서 수행한 모든 작업이 포함될 수 있습니다.

프로세스 도출을 수행하려면 데이터 세트에 다음 3가지 유형의 정보가 포함되어야 합니다.

케이스 ID — 프로세스를 진행하는 엔티티의 고유 식별자입니다. 일반적인 예는 사용자의 고유 ID일 수 있지만 많은 가능성이 유효합니다(사용 사례에 따라 다름).

이벤트 — 프로세스의 한 단계, 분석 중인 프로세스의 일부인 모든 활동.

타임스탬프 - 성능 평가 및 이벤트 순서 결정에 사용되며 사용자가 주어진 이벤트에 시작/종료(또는 둘 다 실제로)가 될 수 있습니다.

For this article, we use a toy dataset provided by pm4py’s authors.

You can download the dataset (running-example file) from their GitHub[https://github.com/pm4py/pm4py-core/tree/release/tests/input_data]. First, we load all the required libraries.


```python
# data
import pandas as pd
from pm4py.objects.conversion.log import converter as log_converter
from pm4py.objects.log.importer.xes import importer as xes_importer
from pm4py.objects.log.util import dataframe_utils

# process mining
from pm4py.algo.discovery.alpha import algorithm as alpha_miner
from pm4py.algo.discovery.inductive import algorithm as inductive_miner
from pm4py.algo.discovery.heuristics import algorithm as heuristics_miner
from pm4py.algo.discovery.dfg import algorithm as dfg_discovery

# viz
from pm4py.visualization.petrinet import visualizer as pn_visualizer
from pm4py.visualization.process_tree import visualizer as pt_visualizer
from pm4py.visualization.heuristics_net import visualizer as hn_visualizer
from pm4py.visualization.dfg import visualizer as dfg_visualization

# misc
from pm4py.objects.conversion.process_tree import converter as pt_converter

```

    C:\Users\MyCom\anaconda3\lib\site-packages\pm4py\visualization\petrinet\__init__.py:20: UserWarning: please use the pm4py.visualization.petri_net package instead
      warnings.warn("please use the pm4py.visualization.petri_net package instead")


I must say that importing certain modules is not the cleanest in pm4py, for example compared to scikit-learn. Unless there is a very strong reason for such an organization of the library, I do hope it will be simplified in the future.

Then, we import the data. In process mining, the two most common data formats are CSV and XES. The latter became a standard format for storing event logs. You can read more about it here.


```python
# xes
log = xes_importer.apply('./data/running-example.xes')

# csv
df = pd.read_csv('./data/running-example.csv')
df = dataframe_utils.convert_timestamp_columns_in_df(df)
df = df.sort_values('time:timestamp')

log = log_converter.apply(df)
df.head()
```


    parsing log, completed traces ::   0%|          | 0/6 [00:00<?, ?it/s]





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Activity</th>
      <th>Costs</th>
      <th>Resource</th>
      <th>case:concept:name</th>
      <th>case:creator</th>
      <th>concept:name</th>
      <th>org:resource</th>
      <th>time:timestamp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>14</th>
      <td>register request</td>
      <td>50</td>
      <td>Pete</td>
      <td>1</td>
      <td>Fluxicon Nitro</td>
      <td>register request</td>
      <td>Pete</td>
      <td>2010-12-30 10:02:00+00:00</td>
    </tr>
    <tr>
      <th>9</th>
      <td>register request</td>
      <td>50</td>
      <td>Mike</td>
      <td>2</td>
      <td>Fluxicon Nitro</td>
      <td>register request</td>
      <td>Mike</td>
      <td>2010-12-30 10:32:00+00:00</td>
    </tr>
    <tr>
      <th>10</th>
      <td>check ticket</td>
      <td>100</td>
      <td>Mike</td>
      <td>2</td>
      <td>Fluxicon Nitro</td>
      <td>check ticket</td>
      <td>Mike</td>
      <td>2010-12-30 11:12:00+00:00</td>
    </tr>
    <tr>
      <th>11</th>
      <td>examine casually</td>
      <td>400</td>
      <td>Sean</td>
      <td>2</td>
      <td>Fluxicon Nitro</td>
      <td>examine casually</td>
      <td>Sean</td>
      <td>2010-12-30 13:16:00+00:00</td>
    </tr>
    <tr>
      <th>0</th>
      <td>register request</td>
      <td>50</td>
      <td>Pete</td>
      <td>3</td>
      <td>Fluxicon Nitro</td>
      <td>register request</td>
      <td>Pete</td>
      <td>2010-12-30 13:32:00+00:00</td>
    </tr>
  </tbody>
</table>
</div>



Please pay extra attention to the naming convention presented in the table. There is some repetition in the columns, so you can clearly see how they were renamed to work well with the algorithms in pm4py.

The default name indicating the case ID is case:concept:name, concept:name is the event, and lastly time:timestamp is the corresponding timestamp. In case the columns are not named this way, we can always rename them using the rename method of a pd.DataFrame. See this part of the documentation for more information about correctly indicating the case column.

The benefit of working with this small dataset is that we can actually inspect the entire log and work out what happened in our heads. This way, interpreting the created process maps will be much easier.

You can find more examples of event logs here.

표에 나와 있는 naming convention에 특히 주의하십시오. 열에 약간의 반복이 있으므로 pm4py의 알고리즘과 잘 작동하도록 이름이 어떻게 바뀌었는지 명확하게 알 수 있습니다.

케이스 ID를 나타내는 기본 이름은 case:concept:name, concept:name은 이벤트, 마지막으로 time:timestamp는 해당 타임스탬프입니다. 열 이름이 이런 식으로 지정되지 않은 경우 pd.DataFrame의 이름 바꾸기 메서드를 사용하여 항상 이름을 바꿀 수 있습니다. case 열을 올바르게 표시하는 방법에 대한 자세한 내용은 설명서의 이 부분을 참조하십시오.

이 작은 데이터 세트로 작업할 때의 이점은 전체 로그를 실제로 검사하고 머리 속에서 무슨 일이 일어났는지 알아낼 수 있다는 것입니다. 이렇게 하면 생성된 프로세스 맵을 훨씬 쉽게 해석할 수 있습니다.

여기에서 이벤트 로그의 더 많은 예를 찾을 수 있습니다.

# Alpha Miner

Alpha Miner is one of the best-known process discovery algorithms.

In short, the algorithm scans the traces (sequences in the event log) for ordering relations and builds the footprint matrix.

Then, it converts the matrix into a Petri net (a type of graph). This video contains a more detailed description of the algorithm.

Running Alpha Miner results in the following:

- a Petri net model in which all the transitions are visible, unique, and correspond to the classified events.

- the initial marking — it describes the status of the Petri net model when the execution starts.

- the final marking — it describes the status of the Petri net model when the execution ends.

Process models expressed using Petri nets share a well-defined semantic: the execution of the process starts from the events included in the initial marking and finishes at the events included in the final marking.

Some of the characteristics of the algorithm:

- it cannot handle loops of length one or two,

- invisible and duplicated tasks cannot be discovered,

- the discovered model might not be sound (for a definition of model soundness in process mining, please refer to this video),

- it does not handle noise well.

To implement the algorithm in Python, we run the following code:


# 알파 알고리즘

Alpha Miner는 가장 잘 알려진 프로세스 도출 알고리즘 중 하나입니다.

간단히 말해서, 알고리즘은 순서 관계를 위해 trace(이벤트 로그의 시퀀스)를 스캔하고 발자국 행렬을 만듭니다.

그런 다음 행렬을 페트리넷(그래프의 일종)으로 변환합니다. 이 비디오에는 알고리즘에 대한 자세한 설명이 포함되어 있습니다.

Alpha Miner를 실행하면 다음과 같은 결과가 나타납니다.

- 모든 전환이 표시되고 고유하며 분류된 이벤트에 해당하는 Petri net 모델입니다.

- 초기 마킹 — 실행이 시작될 때 페트리넷 모델의 상태를 설명합니다.

- 최종 마킹 - 실행 종료 시 페트리넷 모델의 상태를 설명합니다.

Petri nets를 사용하여 표현된 프로세스 모델은 잘 정의된 의미를 공유합니다. 프로세스 실행은 초기 마킹에 포함된 이벤트에서 시작하여 최종 마킹에 포함된 이벤트에서 완료됩니다.

알고리즘의 일부 특성:

- 길이가 1 또는 2인 루프를 처리할 수 없습니다.

- 보이지 않는 중복 작업은 발견할 수 없으며,

- 발견된 모델이 정상적이지 않을 수 있음(프로세스 마이닝에서 모델 건전성에 대한 정의는 이 비디오를 참조하십시오),

- noise에 잘 처리하지 못한다.

Python에서 알고리즘을 구현하기 위해 다음 코드를 실행합니다.


```python
import os
os.environ["PATH"]+=os.pathsep+'C:/Program Files/Graphviz/bin/'
```

- ExecutableNotFound: failed to execute WindowsPath('dot'), make sure the Graphviz executables are on your systems' PATH 오류 해결방안

1. !conda install graphviz  설치해준다

2. 시스템 환경 변수 path에 추가

User - C:\Program Files\Graphviz\bin

System - C:\Program Files\Graphviz\bin\dot.exe

3. 다시 에디터로 가서 코드 입력

import os
os.environ["PATH"]+=os.pathsep+'C:/Program Files/Graphviz/bin/'

후에 import graphviz를해주면 실행이 잘 됩니다.

- 참고 사이트 - [https://free-eunb.tistory.com/14](https://free-eunb.tistory.com/14)


```python
# alpha miner
net, initial_marking, final_marking = alpha_miner.apply(log)

# viz
gviz = pn_visualizer.apply(net, initial_marking, final_marking)
pn_visualizer.view(gviz)
```

    <ipython-input-26-b547e5ed9a96>:5: DeprecatedWarning: apply is deprecated as of 2.2.5 and will be removed in 3.0.0. please use pm4py.visualization.petri_net.algorithm instead
      gviz = pn_visualizer.apply(net, initial_marking, final_marking)



![output_15_1]({{ site.gdrive_url_prefix }}10DXysDG-OqTZbIuHFVsLImpZJ63wI9kE)




The green circle represents the initial marking, while the orange one the final marking. Using the 1st case of the dataset, we can follow the trace: register request -> examine thoroughly -> check ticket -> decide -> reject request.

To provide a bit more information on the process map, we can add information about the frequency of the events. We do not need to rerun the algorithm, we just add a parameter to the visualizer.

녹색 원은 초기 마킹을 나타내고 주황색 원은 최종 마킹을 나타냅니다. 데이터 세트의 첫 번째 케이스를 사용하면 trace를 다음과 같이 볼수있습니다. 요청 등록 -> 철저히 조사 -> 티켓 확인 -> 결정 -> 요청 거부.

프로세스 맵에 대한 정보를 조금 더 제공하기 위해서, 이벤트 빈도에 대한 정보를 추가할 수 있습니다. 알고리즘을 다시 실행할 필요가 없으며 시각화 도우미에 매개변수를 추가하면 됩니다.


```python
# add information about frequency to the viz
parameters = {pn_visualizer.Variants.FREQUENCY.value.Parameters.FORMAT: "png"}
gviz = pn_visualizer.apply(net, initial_marking, final_marking,
                           parameters=parameters,
                           variant=pn_visualizer.Variants.FREQUENCY,
                           log=log)

# save the Petri net
pn_visualizer.save(gviz, "alpha_miner_petri_net.png")
# view
pn_visualizer.view(gviz)
```

    <ipython-input-29-505645f17a4d>:3: DeprecatedWarning: apply is deprecated as of 2.2.5 and will be removed in 3.0.0. please use pm4py.visualization.petri_net.algorithm instead
      gviz = pn_visualizer.apply(net, initial_marking, final_marking,



    replaying log with TBR, completed variants ::   0%|          | 0/6 [00:00<?, ?it/s]





![output_17_2]({{ site.gdrive_url_prefix }}1cueBEHN7jcyJCCjB7lOpYndEHuWdlP8h)


This time, we see the most frequently taken steps in the traces.

There is an improved version of the Alpha Miner called Alpha+ Miner, which can additionally handle loops of length one and two.

## Directly-Follows Graph
The second class of process models is the Directly-Follows Graph.

In this class of models, the nodes represent the events from the log, while directed edges connect the nodes if there is at least one trace in the log where the source event is followed by the target event.

These directed edges work nicely together with some additional metrics, such as:

- frequency — the number of times the source event is followed by the target event.

- performance — some kind of aggregation, for example, the average time elapsed between the two events.

- In the most basic variant, we can create a Directly-Follows Graph from the event log by running the following lines of code:

## 그래프를 직접 따라가기
두 번째 클래스의 프로세스 모델은 Directly-Follows Graph입니다.

이 클래스의 모델에서 노드는 로그의 이벤트를 나타내는 반면, 원본 이벤트 다음에 타깃된 이벤트가 오는 로그에 하나 이상의 trace가 있는 경우 방향이 지정된 가장자리가 노드를 연결합니다.

이러한 모서리는 다음과 같은 몇 가지 추가 항목과 함께 잘 작동합니다.

- 빈도 - 원본 이벤트 다음에 타깃된 이벤트가 오는 횟수입니다.

- 성능 - 일종의 집계(예: 두 이벤트 사이에 경과된 평균 시간).

- 가장 기본적인 변형에서 다음 코드 줄을 실행하여 이벤트 로그에서 Directly-Follows Graph를 만들 수 있습니다.


```python
# creatig the graph from log
dfg = dfg_discovery.apply(log)

# viz
gviz = dfg_visualization.apply(dfg, log=log, variant=dfg_visualization.Variants.FREQUENCY)
dfg_visualization.view(gviz)
```




![output_20_0]({{ site.gdrive_url_prefix }}1kUHuXFFTeuWF_ozGXpEF5phxrFwLiUb1)


In this graph, we added the frequency on top of the directed edges.

We can see that this graph is significantly different from the Petri net obtained from the Alpha Miner.

That is because this type of graph shows all the connections and does not try to find some rules that the events follow.

이 그래프에서는 방향이 있는 모서리 위에 빈도를 추가했습니다.

이 그래프가 Alpha Miner에서 얻은 Petri net과 확연히 다른 것을 알 수 있습니다.

이 유형의 그래프는 모든 연결을 보여주고 이벤트가 따르는 일부 규칙을 찾으려고 하지 않기 때문입니다.

Alternatively, we can decorate the edges by using the performance metric instead.

By using the PERFORMANCE variant we display the average time elapsed between the two nodes.

또는 대신 성능 메트릭을 사용하여 가장자리를 장식할 수 있습니다.

PERFORMANCE 변형을 사용하여 두 노드 사이에 경과된 평균 시간을 표시합니다.


```python
# creatig the graph from log
dfg = dfg_discovery.apply(log, variant=dfg_discovery.Variants.PERFORMANCE)

# vizb

gviz = dfg_visualization.apply(dfg, log=log, variant=dfg_visualization.Variants.PERFORMANCE)
dfg_visualization.view(gviz)
```



![output_23_0]({{ site.gdrive_url_prefix }}12sSkl_ggzB28Qi2TKVejP23VyhwivEK3)

# Heuristic Miner

Heuristics Miner is an improvement of the Alpha Miner algorithm and acts on the Directly-Follows Graph.

It provides a way to handle noise and to find common constructs.

The output of the algorithm is a Heuristics Net — an object that contains both the activities and the relationships between them.


Note: The Heuristics Net can be converted into a Petri net.

Some of the characteristics of the algorithm:

- takes frequency into account,

- detects short loops and skipping events,

- does not guarantee that the discovered model will be sound.

We run the Heuristics Miner with all the default settings in the following snippet:

# 휴리스틱 마이너

Heuristics Miner는 Alpha Miner 알고리즘을 개선한 것으로 Directly-Follows Graph에서 작동합니다.

노이즈를 처리하고 공통 구성을 찾는 방법을 제공합니다.

알고리즘의 출력은 활동과 활동 간의 관계를 모두 포함하는 객체인 휴리스틱 네트워크입니다.

참고: 휴리스틱 넷은 페트리 넷으로 변환할 수 있습니다.

알고리즘의 일부 특성:

- 빈도를 고려하고,

- 짧은 루프 및 건너뛰는 이벤트 감지,

- 발견된 모델이 정상임을 보장하지 않습니다.

다음 스니펫의 모든 기본 설정으로 휴리스틱 마이너를 실행합니다.


```python
# heuristics miner
heu_net = heuristics_miner.apply_heu(log)

# viz
gviz = hn_visualizer.apply(heu_net)
hn_visualizer.view(gviz)
```



![output_25_0]({{ site.gdrive_url_prefix }}15KadH3Izn63Lo0QDYzqoa39H07RFlRDM)

The interpretation of the graph is very similar to the Directly-Follows Graph generated above.

We can also see that the connections resemble the flow visualized by Alpha Miner’s Petri net.

It is worth mentioning that the shape of the Heuristics Net is highly dependent on the set of hyperparameters of the algorithm.

Now, let’s generate a Petri net based on the Heuristics Miner.

그래프의 해석은 위에서 생성한 Directly-Follows Graph와 매우 유사합니다.

또한 연결이 Alpha Miner의 Petri net으로 시각화된 흐름과 유사한 것을 볼 수 있습니다.

Heuristics Net의 모양은 알고리즘의 하이퍼파라미터 집합에 크게 의존한다는 점을 언급할 가치가 있습니다.

이제 Heuristics Miner를 기반으로 Petri net을 생성해 보겠습니다.


```python
# heuristics miner
net, im, fm = heuristics_miner.apply(log)

# viz
gviz = pn_visualizer.apply(net, im, fm)
pn_visualizer.view(gviz)
```

    <ipython-input-36-6609aebecd48>:5: DeprecatedWarning: apply is deprecated as of 2.2.5 and will be removed in 3.0.0. please use pm4py.visualization.petri_net.algorithm instead
      gviz = pn_visualizer.apply(net, im, fm)




![output_27_1]({{ site.gdrive_url_prefix }}1yQy-NL4xZz-n3-qVubr6pvEx_So_TDb5)


The generated model is much more complex than the one generated by the Alpha Miner.


# Inductive Miner

The Inductive Miner algorithm is an improvement of both the Alpha Miner and Heuristics Miner.

The biggest difference is that it guarantees a sound process model with good values of fitness (usually assuring perfect replay).

The underlying idea of the algorithm is to find a prominent split in the event log (there are different types of splits: sequential, parallel, concurrent, and loop).

After finding the split, the algorithm recurs on the sub-logs (found by applying the split), until a base case is identified.

The Inductive Miner does not work on Petri nets, but on process trees (we can convert them to Petri nets, we will see an example soon).


The are multiple variants of the algorithm, one of them — IMDFc — avoids the recursion on the sub-logs in favor of using the Directly Follows graph.

To create a process tree from our event log, we run the following code:


# 인덕티브 마이너

Inductive Miner 알고리즘은 Alpha Miner와 Heuristics Miner를 모두 개선한 것입니다.

가장 큰 차이점은 적합성(보통 완벽한 재생을 보장)의 가치가 있는 정상적인 프로세스 모델을 보장한다는 것입니다.

알고리즘의 숨겨진 아이디어는 이벤트 로그에서 두드러진 분할을 찾는 것입니다(순차, 병렬, 동시 및 루프와 같은 다양한 분할 유형이 있음).

분할을 찾은 후 알고리즘은 기본 사례가 식별될 때까지 하위 로그(분할을 적용하여 찾은)에서 반복됩니다.

Inductive Miner는 Petri net에서 작동하지 않지만 프로세스 트리에서 작동합니다(Petri net으로 변환할 수 있으며 곧 예제를 볼 수 있습니다).


알고리즘의 여러 변형이 있으며 그 중 하나인 IMDFc는 Directly Follows 그래프를 사용하기 위해 하위 로그에서 재귀를 피합니다.

이벤트 로그에서 프로세스 트리를 생성하기 위해 다음 코드를 실행합니다.


```python
# create the process tree
tree = inductive_miner.apply_tree(log)

# viz
gviz = pt_visualizer.apply(tree)
pt_visualizer.view(gviz)
```



![output_30_0]({{ site.gdrive_url_prefix }}1OBCeZcOJvCbDWUobH_8ws4pZShO04eGl)

where -> is the sequence operator, X is the exclusive choice and * is the loop.

In the next block of code, we convert the process tree into a more familiar Petri net.


```python
# convert the process tree to a petri net
net, initial_marking, final_marking = pt_converter.apply(tree)

# alternatively, use the inductive_miner to create a petri net from scratch
# net, initial_marking, final_marking = inductive_miner.apply(log)

# viz
parameters = {pn_visualizer.Variants.FREQUENCY.value.Parameters.FORMAT: "png"}
gviz = pn_visualizer.apply(net, initial_marking, final_marking,
                           parameters=parameters,
                           variant=pn_visualizer.Variants.FREQUENCY,
                           log=log)
pn_visualizer.view(gviz)
```

    <ipython-input-39-dacf76f90653>:9: DeprecatedWarning: apply is deprecated as of 2.2.5 and will be removed in 3.0.0. please use pm4py.visualization.petri_net.algorithm instead
      gviz = pn_visualizer.apply(net, initial_marking, final_marking,



    replaying log with TBR, completed variants ::   0%|          | 0/6 [00:00<?, ?it/s]





![output_32_2]({{ site.gdrive_url_prefix }}1GDkzM-9m0cSVlxWx_J5x1TZ5Moq_38lv)

# Conclusions

In this article, I gave a brief overview of process mining as a useful technique to use in business.

I do think that it is definitely not a mainstream skill in the typical data scientist’s skillset, but successfully implementing process mining can actually generate significant value-added for many companies.


# 결론

이 기사에서는 비즈니스에서 사용하는 유용한 기술인 프로세스 마이닝에 대한 간략한 개요를 제공했습니다.

일반적인 데이터 과학자의 기술에서 주류 기술은 아니지만 프로세스 마이닝을 성공적으로 구현하면 실제로 많은 회사에서 상당한 부가가치를 창출할 수 있다고 생각합니다.

