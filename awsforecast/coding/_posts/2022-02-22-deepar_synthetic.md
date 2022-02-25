---
layout: single
title: "Time series forecasting with DeepAR - Synthetic data"
date: "2022-02-25 16:39:32 +0900"
last_modified_at: "2022-02-25 16:39:32 +0900"
---

# Time series forecasting with DeepAR - Synthetic data

DeepAR is a supervised learning algorithm for forecasting scalar time series. This notebook demonstrates how to prepare a dataset of time series for training DeepAR and how to use the trained model for inference.

This notebook was tested in Amazon SageMaker Studio on ml.t3.medium instance with Python 3 (Data Science) kernel.


```python
import time
import numpy as np
import pandas as pd
import json #JSON (JavaScript Object Notation) 은 XML, YAML 과 함께 효율적으로 데이터를 저장하고 교환(exchange data)하는데 사용하는 텍스트 데이터 포맷
import matplotlib.pyplot as plt

# Boto3를 사용하면 Python 애플리케이션, 라이브러리 또는 스크립트를 Amazon S3, Amazon EC2, Amazon DynamoDB 등 AWS 서비스와 쉽게 통합할 수 있습니다
import boto3
import sagemaker

# get_execution_role : 초기에 설정해 뒀던 IAM role 가져오기
# IAM 역할 : AWS에서 자격증명할때 필요함

from sagemaker import get_execution_role

np.random.seed(1)
```

Let's start by specifying:
- The S3 bucket prefix that you want to use for training and model data. Here we use the default bucket with `sagemaker_session.default_bucket()`, but you can change this to a bucket of your choosing. This should be within the same region as the Notebook Instance, training, and hosting.
- The IAM role arn used to give training and hosting access to your data. See the documentation for how to create these. Here we use the `get_execution_role` function to obtain the role arn which was specified when creating the notebook.


```python
prefix = "sagemaker/DEMO-deepar"

# 방문자가 웹 서버에 접속해 있는 상태를 하나의 단위로 보고 그것을 세션이라고 한다.

sagemaker_session = sagemaker.Session()

role = get_execution_role() # IAM 자격증명 가져옴

bucket = sagemaker_session.default_bucket() # S3 Bucket

s3_data_path = f"{bucket}/{prefix}/data" # S3 Bucket 데이터 경로 설정 
s3_output_path = f"{bucket}/{prefix}/output"
```

Next, we configure the container image to be used for the region that we are running in.

# 훈련 작업 생성 및 실행

내장 Amazon SageMaker 알고리즘은 Amazon Elastic Container Registry(Amazon ECR)에 Docker 컨테이너로 저장됩니다. 모델을 훈련시키려면 먼저 Amazon ECR에서 해당 리전에 가장 가까운 forecasting-deepa 컨테이너 위치를 지정해야 합니다.


```python
from sagemaker.amazon.amazon_estimator import get_image_uri

# 이미지 가져오기
image_uri = get_image_uri(boto3.Session().region_name, "forecasting-deepar")
```

    The method get_image_uri has been renamed in sagemaker>=2.
    See: https://sagemaker.readthedocs.io/en/stable/v2.html for details.
    Defaulting to the only supported framework/algorithm version: 1. Ignoring framework/algorithm version: 1.


### Generating and uploading data

In this example we want to train a model that can predict the next 48 points of syntheticly generated time series.
The time series that we use have hourly granularity.


```python
freq = "H" # hour 시간을 의미한다. 시간으로 설정한다. 1시간 기준이다.

# 48시간 이후 예측 할 수 있는 모델을 훈련시킬 것이다.
prediction_length = 48 # The prediction_length is fixed when a model is trained and it cannot be changed later
```

We also need to configure the so-called `context_length`, which determines how much context of the time series the model should take into account when making the prediction, i.e. how many previous points to look at. A typical value to start with is around the same size as the `prediction_length`. In our example we will use a longer `context_length` of `72`. Note that in addition to the `context_length` the model also takes into account the values of the time series at typical seasonal windows e.g. for hourly data the model will look at the value of the series 24h ago, one week ago one month ago etc. So it is not necessary to make the `context_length` span an entire month if you expect monthly seasonalities in your hourly data.

context_length	파라미터 이름 

네트워크가 어느 시점까지 볼 수 있는지 결정합니다. HPO 프로세스는 다음에 대한 값을 자동으로 설정합니다.context_length모델 정확도를 극대화하는 동시에 훈련 시간을 고려합니다.

context_length 하이퍼파라미터는 과거 네트워크에서 확인 가능한 거리를 제어하며, prediction_length 하이퍼파라미터는 향후 예상할 수 있는 범위를 제어합니다.
과거 72일 데이터셋 패턴으로 예측하기 위해 아래와 같이 변수 설정함


```python
context_length = 72
```

For this notebook, we will generate 200 noisy time series, each consisting of 400 data points and with seasonality of 24 hours. In our dummy example, all time series start at the same time point `t0`. When preparing your data, it is important to use the correct start point for each time series, because the model uses the time-point as a frame of reference, which enables it to learn e.g. that weekdays behave differently from weekends.


```python
t0 = "2016-01-01 00:00:00"# 시작시간
data_length = 400 #데이터 행 수
num_ts = 200 # noisy time 설정
period = 24 # 24시간
```

Each time series will be a noisy sine wave with a random level. 

> noise 처리

>> 노이즈(noise)라고도 불리는 이 데이터는 추세, 계절성 등으로 설명되지 않은 데이터를 의미한다. 이러한 데이터를 가지고 예측하게 된다면 예측의 오차가 커지기 때문에 전처리를 통해서 최대한 예측하는데 관여하지 않도록 하는 것이 중요하다.
랜덤함수 적용 시켜준다. 추세라는 것은 말 그대로 경향을 의미한다. 세부적인 데이터를 다 빼고 전체적으로 보았을 때 주식이 감소하는지 증가하는지 대략적인 정보를 보여준다. 계절성(Seasonality)은 특정한 기간마다 어떤 패턴을 가지고 반복하는지 확인 할 수 있는 특성이다. 이 데이터를 통해 앞으로 어떻게 변화할 것인지 예측할 수 있다.
실제 데이터에는 노이즈를 잘 찾아 적용시켜주어야한다. 밑에 함수는 임의적인 노이즈 데이터를 적용시켜 주었다. 


```python
time_series = []
# num_ts = 200 # noisy time 설정 / 위에서 설정해준값 200 범위에서 k값 나오게 for문 돌림
for k in range(num_ts):  
    level = 10 * np.random.rand() # 0~1 사이의 값을 10을 곱해 지면서 1~10 사이의 값들이 만들어 집니다.
    seas_amplitude = (0.1 + 0.3 * np.random.rand()) * level # y 축 값들을 2.5 ~ 6.0 으로 설정함
    sig = 0.05 * level  # noise parameter (constant in time)  
    time_ticks = np.array(range(data_length)) # 배열로 만듬
    
    source = level + seas_amplitude * np.sin(time_ticks * (2 * np.pi) / period) #  삼각함수 사인 값 (trigonometric sine)을 반환하는 함수 적용
    noise = sig * np.random.randn(data_length) # 임의적으로 노이즈를 만들어줍니다,
    data = source + noise # 실제 데이터에는 노이즈가 포함된다. 임의적으로 적용시켜주었습니다. 
    
    # 위에서 설정해준 변수 t0 (시작값), freq 변수 시간으로 정한 값을 적용시켜줍니다.
    index = pd.date_range(start=t0, freq=freq, periods=data_length)
    
    time_series.append(pd.Series(data=data, index=index)) # 시계열 데이터는 series 를 적용시켜 줌
```


```python
time_series[0].plot()
plt.show()
```

![output_20_0]({{ site.gdrive_url_prefix }}1xl7MV-v0HJVGUZ7E9F7QsZDk8ThobIg3)


Often one is interested in tuning or evaluating the model by looking at error metrics on a hold-out set. For other machine learning tasks such as classification, one typically does this by randomly separating examples into train/test sets. For forecasting it is important to do this train/test split in time rather than by series.

In this example, we will leave out the last section of each of the time series we just generated and use only the first part as training data. Here we will predict 48 data points, therefore we take out the trailing 48 points from each time series to define the training set. The test set contains the full range of each time series.


```python
time_series_training = [] # 훈련데이터 리스트 만들어줌
for ts in time_series:
    time_series_training.append(ts[:-prediction_length]) # 48시간 이후 예측 할 수 있는 모델을 훈련 시키기 위해 48시간 전까지 훈련데이터로 만들어 준다.
```


```python
time_series[0].plot(label="test") # 테스트 데이터 시각화
time_series_training[0].plot(label="train", ls=":") # .... 으로 훈련데이터 시각화 해줌
plt.legend()
plt.show()
```



![output_23_0]({{ site.gdrive_url_prefix }}1iA1jIj306Z3U4s50P5oGJBPVhErx4j1J)



The following utility functions convert `pandas.Series` objects into the appropriate JSON strings that DeepAR can consume. We will use these to write the data to S3.

# DeepAR preprocessing

 > DeepAR supports two types of data files: JSON Lines (one JSON object per line) and Parquet.

- start : A string with the format YYYY-MM-DD HH:MM:SS

- target : An array of floating-point values or integers that represent the time series. You can encode missing values as null literals, or as “NaN” strings in JSON,

- cat(선택 사항) : An array of categorical features that can be used to encode the groups that the record belongs to. Categorical features must be encoded as a 0-based sequence of positive integers. For example, the categorical domain {R, G, B} can be encoded as {0, 1, 2}. All values from each categorical domain must be represented in the training dataset. That's because the DeepAR algorithm can forecast only for categories that have been observed during training.
 

## DeepAR 알고리즘을 사용하기 위해 JSON 형식으로 만들어 줄 수 있는 함수 생성

- JSON 


1. XML, YAML 과 함께 효율적으로 데이터를 저장하고 교환하는데 사용하는 텍스트 데이터 포맷

2. 구조 

  2.1 이름/값 쌍의 집합 (A collection of name/value pairs): object, record, struct, dictionary, hash table, keyed list, associative array
  
  2.2 정렬된 값의 리스트 (An ordered list of values): array, vector, list, sequence


    
![20220222_185322_1]({{ site.gdrive_url_prefix }}1xFdlRdihlNE0MSqx08SXgUBe_8enNnHZ)




- 사진 출처 :[https://rfriend.tistory.com/474](https://rfriend.tistory.com/474)

"https://ai-example.notebook.ap-northeast-2.sagemaker.aws/view/amazon-sagemaker-examples/synthetic_data/data/20220222_185322_1.png"


```python
def series_to_obj(ts, cat=None):
    ''' 
    obj 변수는 dict으로 구성되어 있다. python object를 json으로 변환시키기 위해 
    json.dumps 함수 적용시킴  
    '''
    obj = {"start": str(ts.index[0]), "target": list(ts)} # start는 string 이여야 함, ts는 데이터를 담은 변수이다.
    if cat is not None: # cat default 값이 None인데 None 이 아닐때 실행됨
        obj["cat"] = cat
    return obj

# json 형태로 반환 할 수 있는 함수
def series_to_jsonline(ts, cat=None):
    return json.dumps(series_to_obj(ts, cat))
```

** client **

1. low-level 인터페이스

2. service description에 의해 만들어짐

3. botocore 수준의 client를 공개(botocore는 AWS CLI와 boto3의 기초가 되는 라이브러리)

4. AWS API와 1:1 매핑됨
    
5. 메소드가 스네이크 케이스로 정의되어 있음


```python
encoding = "utf-8"
FILE_TRAIN = "train.json"
FILE_TEST = "test.json"

# 훈련데이터 json 형태로 저장
# 바이너리 파일형태로 써주고 저장한다.
with open(FILE_TRAIN, "wb") as f:
    for ts in time_series_training:
        f.write(series_to_jsonline(ts).encode(encoding)) #json 형태로 반환 할 수 있는 함수 적용/ encode 적용 
        f.write("\n".encode(encoding))

# 테스트데이터 json 형태로 저장
with open(FILE_TEST, "wb") as f:
    for ts in time_series:
        f.write(series_to_jsonline(ts).encode(encoding))
        f.write("\n".encode(encoding))

# AWS s3인 버킷에 파일 업로드 함        
s3 = boto3.client("s3")

# prefix = "sagemaker/DEMO-deepar" 위에서 경로 설정한거 변수 넣어줌
s3.upload_file(FILE_TRAIN, bucket, prefix + "/data/train/" + FILE_TRAIN)
s3.upload_file(FILE_TEST, bucket, prefix + "/data/test/" + FILE_TRAIN)
```

### Train a model

We can now define the estimator that will launch the training job.

- 출처 : [https://sagemaker.readthedocs.io/en/stable/api/training/estimators.html](https://sagemaker.readthedocs.io/en/stable/api/training/estimators.html)

1. sagemaker_session (sagemaker.session.Session) – Session object which manages interactions with Amazon SageMaker APIs and any other AWS services needed. If not specified, the estimator creates one using the default AWS configuration chain.

2. image_uri (str) – The container image to use for training.

3. role (str) – An AWS IAM role (either name or full ARN). The Amazon SageMaker training jobs and APIs that create Amazon SageMaker endpoints use this role to access training data and model artifacts. After the endpoint is created, the inference code might use the IAM role, if it needs to access an AWS resource.

4. instance_count (int) – Number of Amazon EC2 instances to use for training.

- What is an Amazon EC2 instance?

An Amazon EC2 instance is a virtual server in Amazon's Elastic Compute Cloud (EC2) for running applications on the Amazon Web Services (AWS) infrastructure. AWS is a comprehensive, evolving cloud computing platform; EC2 is a service that enables business subscribers to run application programs in the computing environment. It can serve as a practically unlimited set of virtual machines (VMs).

5. base_job_name (str) – Prefix for training job name when the fit() method launches. If not specified, the estimator generates a default job name, based on the training image name and current timestamp.




```python
estimator = sagemaker.estimator.Estimator(
    sagemaker_session=sagemaker_session,
    image_uri=image_uri, # 훈련 하기 위해 설정함, 위에서 변수로 설정한 forecasting-deepar으로 훈련시킴
    role=role,  # AWS IAM role , 위에서 변수로 설정한 IAM 자격증명 가져옴
    instance_count=1, # EC2 instances 숫자 , 가상환경 몇개를 사용할지 설정함, 가상환경 1개를 사용함.
    instance_type="ml.m5.2xlarge", # 노트북 인스턴스 생성할때 설정 했던 노트북 인스턴스 유형 ml.m5.2xlarge으로 설정
    base_job_name="DEMO-deepar", # deepar default 값 
    output_path=f"s3://{s3_output_path}",  # 위에서 설정한 변수 f"{bucket}/{prefix}/output" 넣어줌
)
```

Next we need to set some hyperparameters: for example, frequency of the time series used, number of data points the model will look at in the past, number of predicted data points. The other hyperparameters concern the model to train (number of layers, number of cells per layer, likelihood function) and the training options such as number of epochs, batch size, and learning rate. Refer to the documentation for a full description of the available parameters.

* DeepAR Parameters

freq: Frequency at which data is sampled , for our task we have sampled it monthwise

prediction_length: Corresponds to how many time steps into the future we have to make predictions , in this case it 10 months in future

context_length: Corresponds to how many observations we will be looking back while making future preds in this case 42 months

use_feat_static_cat: This is one of the most important parameter while doing Multiple Time Series we need some indicator variable to tell our model which time series observation is which , we earlier had made variable ts_code this stores the item_id in form of integers which are used to tell our model at run time to which time series an observation belongs to hence we give use_feat_static_cat as True

cardinality: While making preds we are only using the returns observation for each item month wise and no other extra features , this is cardinality , hence it is 1

num_layers: Corresponds to number of RNN Layers in our NN

num_cells: Corresponds to number of cells in each RNN

cell_type: What is cell_type here it is LSTM which is RNN , we can also use GRU

Trainer: A Trainer object to be used , currently using Default Trainer Object with 15 epochs

- mini_batch_size

- 출처:[https://welcome-to-dewy-world.tistory.com/86](https://welcome-to-dewy-world.tistory.com/86)

데이터를 1개씩 입력받아 총 10만번의 연산을 진행하는 것보다 한번에 큰 묶음으로 데이터로 입력받아(Batch) n번의 연산을 진행하는 것이 더 빠르다. 즉, 느린 I/O를 통해 데이터를 읽는 횟수를 줄이고 CPU나 GPU로 순수 계산을 하는 비율을 높여 속도를 빠르게 할 수 있다는 뜻이다. 또한 수치 계산 라이브러리 대부분이 큰 배열을 효율적으로 처리할 수 있도록 고도로 최적화되어 있기 때문에 Batch(묶음)로 데이터를 입력받아 학습시키는 것이 속도 측면에서 효율적이다.

- 미니배치 (Mini-Batch)

미니배치를 설명하기에 앞서 데이터를 하나씩 학습시키는 방법과 전체를 학습시키는 방법의 장단점에 대해 소개하겠다.

 

데이터를 하나 하나씩 신경망에 넣어 학습을 시키면 우선 장점으로는 신경망을 한번 학습시키는데 소요되는 시간이 매우 짧다는 것이다. 하지만 데이터를 하나씩 입력받으면 계산 속도가 사실상 늦어진다. 우리는 대부분 딥러닝에 GPU를 많이 사용하는데 그 이유가 GPU의 병렬처리에 있다. 하지만 데이터를 한개씩 넣어 인공지능을 학습시키면 사실상 GPU의 병렬처리를 사용하지 않으니 그만큼 자원 낭비가 된다. 또한 오차를 줄이기 위해 사용하는 Loss Function에서 최적의 파라미터를 설정하는데 상당히 많이 헤매게 된다.

 

전체 데이터를 입력하는 경우는 한번에 여러개의 데이터에 대해서 신경망을 학습시킬 수 있으므로 오차를 줄일 수 있는 cost function의 최적의 parameter를 하나씩 학습하는것보다 빠르게 알아낼 수 있다. 하지만 전체 데이터를 학습시키기 때문에 신경망을 한 번 학습시키는데 소요되는 시간이 매우 길다.

 

위의 문제점과 장점을 적절히 섞은 것이 바로 미니 배치이다. 미니 배치는 SGD(Stochastic Gradient Descent : 확률적 경사 하강법)와 배치를 섞은 것으로 전체 데이터를 N등분하여 각각의 학습 데이터를 배치 방식으로 학습시킨다. 따라서 최대한 신경망을 한 번 학습시키는데(iteration) 걸리는 시간을 줄이면서 전체 데이터를 반영할 수 있게되며 효율적으로 CPU와 GPU를 활용할 수 있게 된다.

- 학습률(Learning Rate)

- 출처:[https://codingrabbit.tistory.com/11](https://codingrabbit.tistory.com/11)   

앞의 경사하강법에서 시작점에서 다음점으로 이동할 때 보폭(학습률) 만큼 이동한 점을 다음점으로 정한다고 하였습니다. 학습률인란, 현재점에서 다음점으로 얼만큼 이동할지, 다르게 말하면 모델이 얼마나 세세하게 학습을 할지를 말합니다.



학습률이 작다면 손실이 최적인 가중치를 찾는데 오랜 시간이 걸릴 것이며, 학습률이 너무 크다면 최적점을 무질서하게 이탈할 수도 있습니다.


![20220225_141334_1]({{ site.gdrive_url_prefix }}1RDdWaDCj9VUaIabQdtqilNazFJClxDM3)


- 드롭아웃 

드롭아웃(dropout)은 신경망에서 가장 효과적이고 널리 사용하는 규제 기법 중 하나입니다. 토론토(Toronto) 대학의 힌튼(Hinton)과 그의 제자들이 개발했습니다. 드롭아웃을 층에 적용하면 훈련하는 동안 층의 출력 특성을 랜덤하게 끕니다(즉, 0으로 만듭니다). 훈련하는 동안 어떤 입력 샘플에 대해 [0.2, 0.5, 1.3, 0.8, 1.1] 벡터를 출력하는 층이 있다고 가정해 보죠. 드롭아웃을 적용하면 이 벡터에서 몇 개의 원소가 랜덤하게 0이 됩니다. 예를 들면, [0, 0.5, 1.3, 0, 1.1]가 됩니다. "드롭아웃 비율"은 0이 되는 특성의 비율입니다. 보통 0.2에서 0.5 사이를 사용합니다. 테스트 단계에서는 어떤 유닛도 드롭아웃하지 않습니다. 훈련 단계보다 더 많은 유닛이 활성화되기 때문에 균형을 맞추기 위해 층의 출력 값을 드롭아웃 비율만큼 줄입니다.


드롭아웃을 추가하면 기준 모델보다 확실히 향상된다.

정리하면 신경망에서 과대적합을 방지하기 위해 가장 널리 사용하는 방법은 다음과 같습니다:

더 많은 훈련 데이터를 모읍니다.

네트워크의 용량을 줄입니다.

가중치 규제를 추가합니다.

드롭아웃을 추가합니다.

- Early Stopping 이란 무엇인가? 



딥러닝을 비롯한 머신러닝 모델의 한 가지 중요한 딜레마는 다음과 같다. 



너무 많은 Epoch 은 overfitting 을 일으킨다. 하지만 너무 적은 Epoch 은 underfitting 을 일으킨다. 



이런 상황에서 Epoch 을 어떻게 설정해야하는가? 

Epoch 을 정하는데 많이 사용되는 Early stopping 은 무조건 Epoch 을 많이 돌린 후, 특정 시점에서 멈추는 것이다. 



그 특정시점을 어떻게 정하느냐가 Early stopping 의 핵심이라고 할 수 있다. 일반적으로 hold-out validation set 에서의 성능이 더이상 증가하지 않을 때 학습을 중지시키게 된다.   





```python
hyperparameters = {
    "time_freq": freq, # 위에서 설정한 변수 freq = "H" # hour 시간을 의미한다. 
    "context_length": str(context_length),  # context_length = 72으로 위에서 설정한 변수 값을 넣어줌
    "prediction_length": str(prediction_length),   # prediction_length = 48
    "num_cells": "40",  # RNN cells의 수를 40으로 설정함
    "num_layers": "3", # RNN Layers의 수를 3으로 설정함
    "likelihood": "gaussian", # Gaussian model: mean과 variance를 neural networks의 output으로 두고 variance가 양이 되도록 variance output에 softplus를 적용한다. MSE를 구하는 것과 거의 같으나 분모에 variance term이 붙기 때문에 training 안정성은 조금 떨어지는 결과를 보여줌
    "epochs": "20", # 전체 데이터 20번 학습시킴
    "mini_batch_size": "32", #  미니 배치(mini batch): 전체 데이터 셋을 몇 개의 데이터 셋으로 나누었을 때, 그 작은 데이터 셋 뭉치 / 32개씩 나누어써 계산한다.
    "learning_rate": "0.001", # 적절한 학습률을 설정함
    "dropout_rate": "0.05", # 0이 되는 특성의 비율을 설정함
    "early_stopping_patience": "10", 
}
```


```python
estimator.set_hyperparameters(**hyperparameters) # estimator에 hyperparameters 적용시킴
```

We are ready to launch the training job. SageMaker will start an EC2 instance, download the data from S3, start training the model and save the trained model.

If you provide the `test` data channel, as we do in this example, DeepAR will also calculate accuracy metrics for the trained model on this test data set. This is done by predicting the last `prediction_length` points of each time series in the test set and comparing this to the actual value of the time series. The computed error metrics will be included as part of the log output.

**Note:** the next cell may take a few minutes to complete, depending on data size, model complexity, and training options.


```python
data_channels = {"train": f"s3://{s3_data_path}/train/", "test": f"s3://{s3_data_path}/test/"}

estimator.fit(inputs=data_channels) # estimator에 train , test 모델 적용시킴
```

    2022-02-25 07:08:10 Starting - Starting the training job...
    2022-02-25 07:08:34 Starting - Preparing the instances for trainingProfilerReport-1645772890: InProgress
    ......
    2022-02-25 07:09:34 Downloading - Downloading input data
    2022-02-25 07:09:34 Training - Downloading the training image........[34mArguments: train[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736 integration.py:592] worker started[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Reading default configuration from /opt/amazon/lib/python3.6/site-packages/algorithm/resources/default-input.json: {'_kvstore': 'auto', '_num_gpus': 'auto', '_num_kv_servers': 'auto', '_tuning_objective_metric': '', 'cardinality': 'auto', 'dropout_rate': '0.10', 'early_stopping_patience': '', 'embedding_dimension': '10', 'learning_rate': '0.001', 'likelihood': 'student-t', 'mini_batch_size': '128', 'num_cells': '40', 'num_dynamic_feat': 'auto', 'num_eval_samples': '100', 'num_layers': '2', 'test_quantiles': '[0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9]'}[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Merging with provided configuration from /opt/ml/input/config/hyperparameters.json: {'context_length': '72', 'dropout_rate': '0.05', 'early_stopping_patience': '10', 'epochs': '20', 'learning_rate': '0.001', 'likelihood': 'gaussian', 'mini_batch_size': '32', 'num_cells': '40', 'num_layers': '3', 'prediction_length': '48', 'time_freq': 'H'}[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Final configuration: {'_kvstore': 'auto', '_num_gpus': 'auto', '_num_kv_servers': 'auto', '_tuning_objective_metric': '', 'cardinality': 'auto', 'dropout_rate': '0.05', 'early_stopping_patience': '10', 'embedding_dimension': '10', 'learning_rate': '0.001', 'likelihood': 'gaussian', 'mini_batch_size': '32', 'num_cells': '40', 'num_dynamic_feat': 'auto', 'num_eval_samples': '100', 'num_layers': '3', 'test_quantiles': '[0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9]', 'context_length': '72', 'epochs': '20', 'prediction_length': '48', 'time_freq': 'H'}[0m
    [34mProcess 1 is a worker.[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Detected entry point for worker worker[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Using early stopping with patience 10[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] random_seed is None[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] [cardinality=auto] `cat` field was NOT found in the file `/opt/ml/input/data/train/train.json` and will NOT be used for training.[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] [num_dynamic_feat=auto] `dynamic_feat` field was NOT found in the file `/opt/ml/input/data/train/train.json` and will NOT be used for training.[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Training set statistics:[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Real time series[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] number of time series: 200[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] number of observations: 70400[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] mean target length: 352.0[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] min/mean/max target: 0.0040314337238669395/4.920437259127132/14.95400619506836[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] mean abs(target): 4.920437259127132[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] contains missing values: no[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Small number of time series. Doing 2 passes over dataset with prob 0.8 per epoch.[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Test set statistics:[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Real time series[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] number of time series: 200[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] number of observations: 80000[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] mean target length: 400.0[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] min/mean/max target: 0.0040314337238669395/4.917104725933075/15.022744178771973[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] mean abs(target): 4.917104725933075[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] contains missing values: no[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] #memory_usage::<batchbuffer> = 3.5982131958007812 mb[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] nvidia-smi took: 0.02516651153564453 secs to identify 0 gpus[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Number of GPUs being used: 0[0m
    [34m[02/25/2022 07:10:48 INFO 139844299908736] Create Store: local[0m
    [34m#metrics {"StartTime": 1645773048.9825969, "EndTime": 1645773049.2780855, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"get_graph.time": {"sum": 294.175386428833, "count": 1, "min": 294.175386428833, "max": 294.175386428833}}}[0m
    [34m[02/25/2022 07:10:49 INFO 139844299908736] Number of GPUs being used: 0[0m
    [34m[02/25/2022 07:10:49 INFO 139844299908736] #memory_usage::<model> = 38 mb[0m
    [34m#metrics {"StartTime": 1645773049.278172, "EndTime": 1645773049.7575011, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"initialize.time": {"sum": 774.7776508331299, "count": 1, "min": 774.7776508331299, "max": 774.7776508331299}}}[0m
    [34m[02/25/2022 07:10:50 INFO 139844299908736] Epoch[0] Batch[0] avg_epoch_loss=2.316095[0m
    [34m[02/25/2022 07:10:50 INFO 139844299908736] #quality_metric: host=algo-1, epoch=0, batch=0 train loss <loss>=2.3160951137542725[0m
    [34m[02/25/2022 07:10:50 INFO 139844299908736] Epoch[0] Batch[5] avg_epoch_loss=1.583442[0m
    [34m[02/25/2022 07:10:50 INFO 139844299908736] #quality_metric: host=algo-1, epoch=0, batch=5 train loss <loss>=1.5834423104921977[0m
    [34m[02/25/2022 07:10:50 INFO 139844299908736] Epoch[0] Batch [5]#011Speed: 207.89 samples/sec#011loss=1.583442[0m
    [34m/opt/amazon/python3.6/lib/python3.6/contextlib.py:99: DeprecationWarning: generator 'local_timer' raised StopIteration
      self.gen.throw(type, value, traceback)[0m
    [34m[02/25/2022 07:10:51 INFO 139844299908736] processed a total of 292 examples[0m
    [34m#metrics {"StartTime": 1645773049.7575946, "EndTime": 1645773051.5706682, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"epochs": {"sum": 20.0, "count": 1, "min": 20, "max": 20}, "update.time": {"sum": 1812.9818439483643, "count": 1, "min": 1812.9818439483643, "max": 1812.9818439483643}}}[0m
    [34m[02/25/2022 07:10:51 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=161.05025335033514 records/second[0m
    [34m[02/25/2022 07:10:51 INFO 139844299908736] #progress_metric: host=algo-1, completed 5.0 % of epochs[0m
    [34m[02/25/2022 07:10:51 INFO 139844299908736] #quality_metric: host=algo-1, epoch=0, train loss <loss>=1.3728791713714599[0m
    [34m[02/25/2022 07:10:51 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:10:51 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_2ebbf82d-11a0-4672-b3bc-83154799b58c-0000.params"[0m
    [34m#metrics {"StartTime": 1645773051.5707483, "EndTime": 1645773051.6205406, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 49.318552017211914, "count": 1, "min": 49.318552017211914, "max": 49.318552017211914}}}[0m
    [34m[02/25/2022 07:10:51 INFO 139844299908736] Epoch[1] Batch[0] avg_epoch_loss=1.293630[0m
    [34m[02/25/2022 07:10:51 INFO 139844299908736] #quality_metric: host=algo-1, epoch=1, batch=0 train loss <loss>=1.2936302423477173[0m
    [34m[02/25/2022 07:10:52 INFO 139844299908736] Epoch[1] Batch[5] avg_epoch_loss=1.044226[0m
    [34m[02/25/2022 07:10:52 INFO 139844299908736] #quality_metric: host=algo-1, epoch=1, batch=5 train loss <loss>=1.0442256530125935[0m
    [34m[02/25/2022 07:10:52 INFO 139844299908736] Epoch[1] Batch [5]#011Speed: 233.59 samples/sec#011loss=1.044226[0m
    [34m[02/25/2022 07:10:53 INFO 139844299908736] processed a total of 297 examples[0m
    [34m#metrics {"StartTime": 1645773051.6206155, "EndTime": 1645773053.1967614, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1576.082706451416, "count": 1, "min": 1576.082706451416, "max": 1576.082706451416}}}[0m
    [34m[02/25/2022 07:10:53 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=188.420168280764 records/second[0m
    [34m[02/25/2022 07:10:53 INFO 139844299908736] #progress_metric: host=algo-1, completed 10.0 % of epochs[0m
    [34m[02/25/2022 07:10:53 INFO 139844299908736] #quality_metric: host=algo-1, epoch=1, train loss <loss>=0.88489910364151[0m
    [34m[02/25/2022 07:10:53 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:10:53 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_4f8887dd-f693-470d-9ee7-6edae14c18d1-0000.params"[0m
    [34m#metrics {"StartTime": 1645773053.1969085, "EndTime": 1645773053.2513883, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 54.056644439697266, "count": 1, "min": 54.056644439697266, "max": 54.056644439697266}}}[0m
    [34m[02/25/2022 07:10:53 INFO 139844299908736] Epoch[2] Batch[0] avg_epoch_loss=0.981265[0m
    [34m[02/25/2022 07:10:53 INFO 139844299908736] #quality_metric: host=algo-1, epoch=2, batch=0 train loss <loss>=0.9812645316123962[0m
    [34m[02/25/2022 07:10:54 INFO 139844299908736] Epoch[2] Batch[5] avg_epoch_loss=0.622232[0m
    [34m[02/25/2022 07:10:54 INFO 139844299908736] #quality_metric: host=algo-1, epoch=2, batch=5 train loss <loss>=0.6222319304943085[0m
    [34m[02/25/2022 07:10:54 INFO 139844299908736] Epoch[2] Batch [5]#011Speed: 208.31 samples/sec#011loss=0.622232[0m
    [34m[02/25/2022 07:10:54 INFO 139844299908736] processed a total of 308 examples[0m
    [34m#metrics {"StartTime": 1645773053.2514575, "EndTime": 1645773054.9533012, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1701.7793655395508, "count": 1, "min": 1701.7793655395508, "max": 1701.7793655395508}}}[0m
    [34m[02/25/2022 07:10:54 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=180.97549772457756 records/second[0m
    [34m[02/25/2022 07:10:54 INFO 139844299908736] #progress_metric: host=algo-1, completed 15.0 % of epochs[0m
    [34m[02/25/2022 07:10:54 INFO 139844299908736] #quality_metric: host=algo-1, epoch=2, train loss <loss>=0.5216166645288467[0m
    [34m[02/25/2022 07:10:54 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:10:55 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_29296b5e-2bda-4a5a-8f43-bcadbf5311bb-0000.params"[0m
    [34m#metrics {"StartTime": 1645773054.9533758, "EndTime": 1645773055.0068238, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 52.91628837585449, "count": 1, "min": 52.91628837585449, "max": 52.91628837585449}}}[0m
    [34m[02/25/2022 07:10:55 INFO 139844299908736] Epoch[3] Batch[0] avg_epoch_loss=0.183683[0m
    [34m[02/25/2022 07:10:55 INFO 139844299908736] #quality_metric: host=algo-1, epoch=3, batch=0 train loss <loss>=0.1836828589439392[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] Epoch[3] Batch[5] avg_epoch_loss=0.255676[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] #quality_metric: host=algo-1, epoch=3, batch=5 train loss <loss>=0.25567640736699104[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] Epoch[3] Batch [5]#011Speed: 210.57 samples/sec#011loss=0.255676[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] processed a total of 310 examples[0m
    [34m#metrics {"StartTime": 1645773055.0068958, "EndTime": 1645773056.6385052, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1631.5429210662842, "count": 1, "min": 1631.5429210662842, "max": 1631.5429210662842}}}[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=189.991479686629 records/second[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] #progress_metric: host=algo-1, completed 20.0 % of epochs[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] #quality_metric: host=algo-1, epoch=3, train loss <loss>=0.26868246123194695[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_ea0705a7-9797-41a2-8b82-2a3559eeccd6-0000.params"[0m
    [34m#metrics {"StartTime": 1645773056.638581, "EndTime": 1645773056.6932905, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 54.305315017700195, "count": 1, "min": 54.305315017700195, "max": 54.305315017700195}}}[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] Epoch[4] Batch[0] avg_epoch_loss=0.131494[0m
    [34m[02/25/2022 07:10:56 INFO 139844299908736] #quality_metric: host=algo-1, epoch=4, batch=0 train loss <loss>=0.13149385154247284[0m
    [34m[02/25/2022 07:10:57 INFO 139844299908736] Epoch[4] Batch[5] avg_epoch_loss=0.188895[0m
    [34m[02/25/2022 07:10:57 INFO 139844299908736] #quality_metric: host=algo-1, epoch=4, batch=5 train loss <loss>=0.18889484911536178[0m
    [34m[02/25/2022 07:10:57 INFO 139844299908736] Epoch[4] Batch [5]#011Speed: 227.85 samples/sec#011loss=0.188895[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] Epoch[4] Batch[10] avg_epoch_loss=0.111592[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] #quality_metric: host=algo-1, epoch=4, batch=10 train loss <loss>=0.018827971816062928[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] Epoch[4] Batch [10]#011Speed: 206.63 samples/sec#011loss=0.018828[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] processed a total of 325 examples[0m
    [34m#metrics {"StartTime": 1645773056.6933594, "EndTime": 1645773058.4578137, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1764.390468597412, "count": 1, "min": 1764.390468597412, "max": 1764.390468597412}}}[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=184.1876428041568 records/second[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] #progress_metric: host=algo-1, completed 25.0 % of epochs[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] #quality_metric: host=algo-1, epoch=4, train loss <loss>=0.11159172307022593[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_e02e5c1f-b9e5-4026-b29b-1bbae9063ce1-0000.params"[0m
    [34m#metrics {"StartTime": 1645773058.4578927, "EndTime": 1645773058.5100687, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 51.676273345947266, "count": 1, "min": 51.676273345947266, "max": 51.676273345947266}}}[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] Epoch[5] Batch[0] avg_epoch_loss=0.426447[0m
    [34m[02/25/2022 07:10:58 INFO 139844299908736] #quality_metric: host=algo-1, epoch=5, batch=0 train loss <loss>=0.426447331905365[0m
    
    2022-02-25 07:10:54 Training - Training image download completed. Training in progress.[34m[02/25/2022 07:10:59 INFO 139844299908736] Epoch[5] Batch[5] avg_epoch_loss=0.025032[0m
    [34m[02/25/2022 07:10:59 INFO 139844299908736] #quality_metric: host=algo-1, epoch=5, batch=5 train loss <loss>=0.025031663477420807[0m
    [34m[02/25/2022 07:10:59 INFO 139844299908736] Epoch[5] Batch [5]#011Speed: 219.55 samples/sec#011loss=0.025032[0m
    [34m[02/25/2022 07:11:00 INFO 139844299908736] processed a total of 316 examples[0m
    [34m#metrics {"StartTime": 1645773058.510144, "EndTime": 1645773060.0937283, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1583.5204124450684, "count": 1, "min": 1583.5204124450684, "max": 1583.5204124450684}}}[0m
    [34m[02/25/2022 07:11:00 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=199.5422698677336 records/second[0m
    [34m[02/25/2022 07:11:00 INFO 139844299908736] #progress_metric: host=algo-1, completed 30.0 % of epochs[0m
    [34m[02/25/2022 07:11:00 INFO 139844299908736] #quality_metric: host=algo-1, epoch=5, train loss <loss>=0.0134497482329607[0m
    [34m[02/25/2022 07:11:00 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:11:00 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_188265ec-8ff4-4514-b946-da50c325b89a-0000.params"[0m
    [34m#metrics {"StartTime": 1645773060.0937996, "EndTime": 1645773060.1340184, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 39.67475891113281, "count": 1, "min": 39.67475891113281, "max": 39.67475891113281}}}[0m
    [34m[02/25/2022 07:11:00 INFO 139844299908736] Epoch[6] Batch[0] avg_epoch_loss=-0.460796[0m
    [34m[02/25/2022 07:11:00 INFO 139844299908736] #quality_metric: host=algo-1, epoch=6, batch=0 train loss <loss>=-0.46079567074775696[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] Epoch[6] Batch[5] avg_epoch_loss=-0.169595[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] #quality_metric: host=algo-1, epoch=6, batch=5 train loss <loss>=-0.1695948593939344[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] Epoch[6] Batch [5]#011Speed: 215.32 samples/sec#011loss=-0.169595[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] Epoch[6] Batch[10] avg_epoch_loss=-0.044823[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] #quality_metric: host=algo-1, epoch=6, batch=10 train loss <loss>=0.10490310788154603[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] Epoch[6] Batch [10]#011Speed: 202.09 samples/sec#011loss=0.104903[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] processed a total of 331 examples[0m
    [34m#metrics {"StartTime": 1645773060.1341069, "EndTime": 1645773061.9101877, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1776.0207653045654, "count": 1, "min": 1776.0207653045654, "max": 1776.0207653045654}}}[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=186.3614625254041 records/second[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] #progress_metric: host=algo-1, completed 35.0 % of epochs[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] #quality_metric: host=algo-1, epoch=6, train loss <loss>=-0.04482305608689785[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:11:01 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_8087c1fd-3612-440c-ae4a-ed5947fa65cf-0000.params"[0m
    [34m#metrics {"StartTime": 1645773061.910254, "EndTime": 1645773061.966592, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 55.9844970703125, "count": 1, "min": 55.9844970703125, "max": 55.9844970703125}}}[0m
    [34m[02/25/2022 07:11:02 INFO 139844299908736] Epoch[7] Batch[0] avg_epoch_loss=-0.192869[0m
    [34m[02/25/2022 07:11:02 INFO 139844299908736] #quality_metric: host=algo-1, epoch=7, batch=0 train loss <loss>=-0.1928691864013672[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] Epoch[7] Batch[5] avg_epoch_loss=-0.089773[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] #quality_metric: host=algo-1, epoch=7, batch=5 train loss <loss>=-0.08977306758364041[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] Epoch[7] Batch [5]#011Speed: 204.97 samples/sec#011loss=-0.089773[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] processed a total of 312 examples[0m
    [34m#metrics {"StartTime": 1645773061.9666755, "EndTime": 1645773063.6649027, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1698.1444358825684, "count": 1, "min": 1698.1444358825684, "max": 1698.1444358825684}}}[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=183.70467833712408 records/second[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] #progress_metric: host=algo-1, completed 40.0 % of epochs[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] #quality_metric: host=algo-1, epoch=7, train loss <loss>=-0.02890467904508114[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] Epoch[8] Batch[0] avg_epoch_loss=-0.189289[0m
    [34m[02/25/2022 07:11:03 INFO 139844299908736] #quality_metric: host=algo-1, epoch=8, batch=0 train loss <loss>=-0.1892886906862259[0m
    [34m[02/25/2022 07:11:04 INFO 139844299908736] Epoch[8] Batch[5] avg_epoch_loss=-0.067901[0m
    [34m[02/25/2022 07:11:04 INFO 139844299908736] #quality_metric: host=algo-1, epoch=8, batch=5 train loss <loss>=-0.06790078400323789[0m
    [34m[02/25/2022 07:11:04 INFO 139844299908736] Epoch[8] Batch [5]#011Speed: 204.35 samples/sec#011loss=-0.067901[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] Epoch[8] Batch[10] avg_epoch_loss=-0.044435[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] #quality_metric: host=algo-1, epoch=8, batch=10 train loss <loss>=-0.016275575011968614[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] Epoch[8] Batch [10]#011Speed: 193.21 samples/sec#011loss=-0.016276[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] processed a total of 323 examples[0m
    [34m#metrics {"StartTime": 1645773063.6650822, "EndTime": 1645773065.5712554, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1905.494213104248, "count": 1, "min": 1905.494213104248, "max": 1905.494213104248}}}[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=169.50119816646063 records/second[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] #progress_metric: host=algo-1, completed 45.0 % of epochs[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] #quality_metric: host=algo-1, epoch=8, train loss <loss>=-0.0444347799162973[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] Epoch[9] Batch[0] avg_epoch_loss=0.301513[0m
    [34m[02/25/2022 07:11:05 INFO 139844299908736] #quality_metric: host=algo-1, epoch=9, batch=0 train loss <loss>=0.30151283740997314[0m
    [34m[02/25/2022 07:11:06 INFO 139844299908736] Epoch[9] Batch[5] avg_epoch_loss=0.102831[0m
    [34m[02/25/2022 07:11:06 INFO 139844299908736] #quality_metric: host=algo-1, epoch=9, batch=5 train loss <loss>=0.10283090981344382[0m
    [34m[02/25/2022 07:11:06 INFO 139844299908736] Epoch[9] Batch [5]#011Speed: 214.19 samples/sec#011loss=0.102831[0m
    [34m[02/25/2022 07:11:07 INFO 139844299908736] processed a total of 290 examples[0m
    [34m#metrics {"StartTime": 1645773065.5713198, "EndTime": 1645773067.1802373, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1608.574628829956, "count": 1, "min": 1608.574628829956, "max": 1608.574628829956}}}[0m
    [34m[02/25/2022 07:11:07 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=180.27196973009183 records/second[0m
    [34m[02/25/2022 07:11:07 INFO 139844299908736] #progress_metric: host=algo-1, completed 50.0 % of epochs[0m
    [34m[02/25/2022 07:11:07 INFO 139844299908736] #quality_metric: host=algo-1, epoch=9, train loss <loss>=-0.1154014240950346[0m
    [34m[02/25/2022 07:11:07 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:11:07 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_4968fff7-8073-427c-a9b3-4a2409a5f801-0000.params"[0m
    [34m#metrics {"StartTime": 1645773067.1803105, "EndTime": 1645773067.2338054, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 53.102731704711914, "count": 1, "min": 53.102731704711914, "max": 53.102731704711914}}}[0m
    [34m[02/25/2022 07:11:07 INFO 139844299908736] Epoch[10] Batch[0] avg_epoch_loss=-0.146316[0m
    [34m[02/25/2022 07:11:07 INFO 139844299908736] #quality_metric: host=algo-1, epoch=10, batch=0 train loss <loss>=-0.14631634950637817[0m
    [34m[02/25/2022 07:11:08 INFO 139844299908736] Epoch[10] Batch[5] avg_epoch_loss=-0.224772[0m
    [34m[02/25/2022 07:11:08 INFO 139844299908736] #quality_metric: host=algo-1, epoch=10, batch=5 train loss <loss>=-0.22477184484402338[0m
    [34m[02/25/2022 07:11:08 INFO 139844299908736] Epoch[10] Batch [5]#011Speed: 207.30 samples/sec#011loss=-0.224772[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] Epoch[10] Batch[10] avg_epoch_loss=-0.155849[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] #quality_metric: host=algo-1, epoch=10, batch=10 train loss <loss>=-0.07314214501529932[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] Epoch[10] Batch [10]#011Speed: 200.29 samples/sec#011loss=-0.073142[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] processed a total of 364 examples[0m
    [34m#metrics {"StartTime": 1645773067.2338717, "EndTime": 1645773069.2492886, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 2015.3522491455078, "count": 1, "min": 2015.3522491455078, "max": 2015.3522491455078}}}[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=180.6041226042454 records/second[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] #progress_metric: host=algo-1, completed 55.0 % of epochs[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] #quality_metric: host=algo-1, epoch=10, train loss <loss>=-0.12133395959002276[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_e01dd4a6-8052-4ab6-ab44-e319b0b991d9-0000.params"[0m
    [34m#metrics {"StartTime": 1645773069.2493603, "EndTime": 1645773069.3032057, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 53.468942642211914, "count": 1, "min": 53.468942642211914, "max": 53.468942642211914}}}[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] Epoch[11] Batch[0] avg_epoch_loss=-0.274687[0m
    [34m[02/25/2022 07:11:09 INFO 139844299908736] #quality_metric: host=algo-1, epoch=11, batch=0 train loss <loss>=-0.2746865749359131[0m
    [34m[02/25/2022 07:11:10 INFO 139844299908736] Epoch[11] Batch[5] avg_epoch_loss=-0.087261[0m
    [34m[02/25/2022 07:11:10 INFO 139844299908736] #quality_metric: host=algo-1, epoch=11, batch=5 train loss <loss>=-0.08726102610429128[0m
    [34m[02/25/2022 07:11:10 INFO 139844299908736] Epoch[11] Batch [5]#011Speed: 208.79 samples/sec#011loss=-0.087261[0m
    [34m[02/25/2022 07:11:10 INFO 139844299908736] processed a total of 290 examples[0m
    [34m#metrics {"StartTime": 1645773069.3032696, "EndTime": 1645773070.9483445, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1645.0095176696777, "count": 1, "min": 1645.0095176696777, "max": 1645.0095176696777}}}[0m
    [34m[02/25/2022 07:11:10 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=176.27907192446284 records/second[0m
    [34m[02/25/2022 07:11:10 INFO 139844299908736] #progress_metric: host=algo-1, completed 60.0 % of epochs[0m
    [34m[02/25/2022 07:11:10 INFO 139844299908736] #quality_metric: host=algo-1, epoch=11, train loss <loss>=0.06326946690678596[0m
    [34m[02/25/2022 07:11:10 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:11 INFO 139844299908736] Epoch[12] Batch[0] avg_epoch_loss=-0.704607[0m
    [34m[02/25/2022 07:11:11 INFO 139844299908736] #quality_metric: host=algo-1, epoch=12, batch=0 train loss <loss>=-0.7046068906784058[0m
    [34m[02/25/2022 07:11:11 INFO 139844299908736] Epoch[12] Batch[5] avg_epoch_loss=-0.140154[0m
    [34m[02/25/2022 07:11:11 INFO 139844299908736] #quality_metric: host=algo-1, epoch=12, batch=5 train loss <loss>=-0.14015381410717964[0m
    [34m[02/25/2022 07:11:11 INFO 139844299908736] Epoch[12] Batch [5]#011Speed: 214.89 samples/sec#011loss=-0.140154[0m
    [34m[02/25/2022 07:11:12 INFO 139844299908736] processed a total of 288 examples[0m
    [34m#metrics {"StartTime": 1645773070.9484198, "EndTime": 1645773072.4431815, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1494.1904544830322, "count": 1, "min": 1494.1904544830322, "max": 1494.1904544830322}}}[0m
    [34m[02/25/2022 07:11:12 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=192.7328586861848 records/second[0m
    [34m[02/25/2022 07:11:12 INFO 139844299908736] #progress_metric: host=algo-1, completed 65.0 % of epochs[0m
    [34m[02/25/2022 07:11:12 INFO 139844299908736] #quality_metric: host=algo-1, epoch=12, train loss <loss>=-0.09334910164276759[0m
    [34m[02/25/2022 07:11:12 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:12 INFO 139844299908736] Epoch[13] Batch[0] avg_epoch_loss=0.061250[0m
    [34m[02/25/2022 07:11:12 INFO 139844299908736] #quality_metric: host=algo-1, epoch=13, batch=0 train loss <loss>=0.06124971807003021[0m
    [34m[02/25/2022 07:11:13 INFO 139844299908736] Epoch[13] Batch[5] avg_epoch_loss=-0.011601[0m
    [34m[02/25/2022 07:11:13 INFO 139844299908736] #quality_metric: host=algo-1, epoch=13, batch=5 train loss <loss>=-0.011601145068804422[0m
    [34m[02/25/2022 07:11:13 INFO 139844299908736] Epoch[13] Batch [5]#011Speed: 211.75 samples/sec#011loss=-0.011601[0m
    [34m[02/25/2022 07:11:14 INFO 139844299908736] processed a total of 317 examples[0m
    [34m#metrics {"StartTime": 1645773072.443255, "EndTime": 1645773074.092655, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1649.0328311920166, "count": 1, "min": 1649.0328311920166, "max": 1649.0328311920166}}}[0m
    [34m[02/25/2022 07:11:14 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=192.2216086281498 records/second[0m
    [34m[02/25/2022 07:11:14 INFO 139844299908736] #progress_metric: host=algo-1, completed 70.0 % of epochs[0m
    [34m[02/25/2022 07:11:14 INFO 139844299908736] #quality_metric: host=algo-1, epoch=13, train loss <loss>=8.765235543251038e-05[0m
    [34m[02/25/2022 07:11:14 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:14 INFO 139844299908736] Epoch[14] Batch[0] avg_epoch_loss=0.098831[0m
    [34m[02/25/2022 07:11:14 INFO 139844299908736] #quality_metric: host=algo-1, epoch=14, batch=0 train loss <loss>=0.09883131831884384[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] Epoch[14] Batch[5] avg_epoch_loss=-0.206959[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] #quality_metric: host=algo-1, epoch=14, batch=5 train loss <loss>=-0.20695899737377962[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] Epoch[14] Batch [5]#011Speed: 229.81 samples/sec#011loss=-0.206959[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] processed a total of 314 examples[0m
    [34m#metrics {"StartTime": 1645773074.0927265, "EndTime": 1645773075.6513138, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1558.1886768341064, "count": 1, "min": 1558.1886768341064, "max": 1558.1886768341064}}}[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=201.50292888155985 records/second[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] #progress_metric: host=algo-1, completed 75.0 % of epochs[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] #quality_metric: host=algo-1, epoch=14, train loss <loss>=-0.1284581024199724[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] best epoch loss so far[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/state_8e361d8b-ee28-4d05-bc00-1218aa47fbd2-0000.params"[0m
    [34m#metrics {"StartTime": 1645773075.6513836, "EndTime": 1645773075.69764, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"state.serialize.time": {"sum": 45.87602615356445, "count": 1, "min": 45.87602615356445, "max": 45.87602615356445}}}[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] Epoch[15] Batch[0] avg_epoch_loss=-0.110106[0m
    [34m[02/25/2022 07:11:15 INFO 139844299908736] #quality_metric: host=algo-1, epoch=15, batch=0 train loss <loss>=-0.1101064682006836[0m
    [34m[02/25/2022 07:11:16 INFO 139844299908736] Epoch[15] Batch[5] avg_epoch_loss=-0.255051[0m
    [34m[02/25/2022 07:11:16 INFO 139844299908736] #quality_metric: host=algo-1, epoch=15, batch=5 train loss <loss>=-0.255051222940286[0m
    [34m[02/25/2022 07:11:16 INFO 139844299908736] Epoch[15] Batch [5]#011Speed: 222.75 samples/sec#011loss=-0.255051[0m
    [34m[02/25/2022 07:11:17 INFO 139844299908736] processed a total of 308 examples[0m
    [34m#metrics {"StartTime": 1645773075.69771, "EndTime": 1645773077.2915208, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1593.7366485595703, "count": 1, "min": 1593.7366485595703, "max": 1593.7366485595703}}}[0m
    [34m[02/25/2022 07:11:17 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=193.2390026838301 records/second[0m
    [34m[02/25/2022 07:11:17 INFO 139844299908736] #progress_metric: host=algo-1, completed 80.0 % of epochs[0m
    [34m[02/25/2022 07:11:17 INFO 139844299908736] #quality_metric: host=algo-1, epoch=15, train loss <loss>=-0.12527945041656494[0m
    [34m[02/25/2022 07:11:17 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:17 INFO 139844299908736] Epoch[16] Batch[0] avg_epoch_loss=-0.138310[0m
    [34m[02/25/2022 07:11:17 INFO 139844299908736] #quality_metric: host=algo-1, epoch=16, batch=0 train loss <loss>=-0.138309508562088[0m
    [34m[02/25/2022 07:11:18 INFO 139844299908736] Epoch[16] Batch[5] avg_epoch_loss=-0.174127[0m
    [34m[02/25/2022 07:11:18 INFO 139844299908736] #quality_metric: host=algo-1, epoch=16, batch=5 train loss <loss>=-0.1741273015116652[0m
    [34m[02/25/2022 07:11:18 INFO 139844299908736] Epoch[16] Batch [5]#011Speed: 206.95 samples/sec#011loss=-0.174127[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] Epoch[16] Batch[10] avg_epoch_loss=-0.035435[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] #quality_metric: host=algo-1, epoch=16, batch=10 train loss <loss>=0.13099481612443925[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] Epoch[16] Batch [10]#011Speed: 206.90 samples/sec#011loss=0.130995[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] processed a total of 323 examples[0m
    [34m#metrics {"StartTime": 1645773077.2916205, "EndTime": 1645773079.1012423, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1809.110164642334, "count": 1, "min": 1809.110164642334, "max": 1809.110164642334}}}[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=178.53093137953854 records/second[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] #progress_metric: host=algo-1, completed 85.0 % of epochs[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] #quality_metric: host=algo-1, epoch=16, train loss <loss>=-0.03543542985889045[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] Epoch[17] Batch[0] avg_epoch_loss=-0.110838[0m
    [34m[02/25/2022 07:11:19 INFO 139844299908736] #quality_metric: host=algo-1, epoch=17, batch=0 train loss <loss>=-0.11083836853504181[0m
    [34m[02/25/2022 07:11:20 INFO 139844299908736] Epoch[17] Batch[5] avg_epoch_loss=-0.195874[0m
    [34m[02/25/2022 07:11:20 INFO 139844299908736] #quality_metric: host=algo-1, epoch=17, batch=5 train loss <loss>=-0.19587354610363641[0m
    [34m[02/25/2022 07:11:20 INFO 139844299908736] Epoch[17] Batch [5]#011Speed: 207.66 samples/sec#011loss=-0.195874[0m
    [34m[02/25/2022 07:11:20 INFO 139844299908736] processed a total of 304 examples[0m
    [34m#metrics {"StartTime": 1645773079.1013107, "EndTime": 1645773080.7758982, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1674.2427349090576, "count": 1, "min": 1674.2427349090576, "max": 1674.2427349090576}}}[0m
    [34m[02/25/2022 07:11:20 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=181.56200963978463 records/second[0m
    [34m[02/25/2022 07:11:20 INFO 139844299908736] #progress_metric: host=algo-1, completed 90.0 % of epochs[0m
    [34m[02/25/2022 07:11:20 INFO 139844299908736] #quality_metric: host=algo-1, epoch=17, train loss <loss>=-0.11305377744138241[0m
    [34m[02/25/2022 07:11:20 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:21 INFO 139844299908736] Epoch[18] Batch[0] avg_epoch_loss=0.031934[0m
    [34m[02/25/2022 07:11:21 INFO 139844299908736] #quality_metric: host=algo-1, epoch=18, batch=0 train loss <loss>=0.0319337323307991[0m
    [34m[02/25/2022 07:11:21 INFO 139844299908736] Epoch[18] Batch[5] avg_epoch_loss=-0.042119[0m
    [34m[02/25/2022 07:11:21 INFO 139844299908736] #quality_metric: host=algo-1, epoch=18, batch=5 train loss <loss>=-0.04211917736877998[0m
    [34m[02/25/2022 07:11:21 INFO 139844299908736] Epoch[18] Batch [5]#011Speed: 207.19 samples/sec#011loss=-0.042119[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] Epoch[18] Batch[10] avg_epoch_loss=-0.046443[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] #quality_metric: host=algo-1, epoch=18, batch=10 train loss <loss>=-0.051630548760294916[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] Epoch[18] Batch [10]#011Speed: 199.13 samples/sec#011loss=-0.051631[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] processed a total of 336 examples[0m
    [34m#metrics {"StartTime": 1645773080.7759795, "EndTime": 1645773082.6062577, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1829.8454284667969, "count": 1, "min": 1829.8454284667969, "max": 1829.8454284667969}}}[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=183.61223445635432 records/second[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] #progress_metric: host=algo-1, completed 95.0 % of epochs[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] #quality_metric: host=algo-1, epoch=18, train loss <loss>=-0.046442528001286766[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] Epoch[19] Batch[0] avg_epoch_loss=-0.250460[0m
    [34m[02/25/2022 07:11:22 INFO 139844299908736] #quality_metric: host=algo-1, epoch=19, batch=0 train loss <loss>=-0.25045979022979736[0m
    [34m[02/25/2022 07:11:23 INFO 139844299908736] Epoch[19] Batch[5] avg_epoch_loss=-0.106722[0m
    [34m[02/25/2022 07:11:23 INFO 139844299908736] #quality_metric: host=algo-1, epoch=19, batch=5 train loss <loss>=-0.10672195442020893[0m
    [34m[02/25/2022 07:11:23 INFO 139844299908736] Epoch[19] Batch [5]#011Speed: 209.54 samples/sec#011loss=-0.106722[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] processed a total of 318 examples[0m
    [34m#metrics {"StartTime": 1645773082.6063247, "EndTime": 1645773084.2362995, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"update.time": {"sum": 1629.6374797821045, "count": 1, "min": 1629.6374797821045, "max": 1629.6374797821045}}}[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] #throughput_metric: host=algo-1, train throughput=195.1219210763308 records/second[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] #progress_metric: host=algo-1, completed 100.0 % of epochs[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] #quality_metric: host=algo-1, epoch=19, train loss <loss>=-0.08941519521176815[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] loss did not improve[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] Final loss: -0.1284581024199724 (occurred at epoch 14)[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] #quality_metric: host=algo-1, train final_loss <loss>=-0.1284581024199724[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] Worker algo-1 finished training.[0m
    [34m[02/25/2022 07:11:24 WARNING 139844299908736] wait_for_all_workers will not sync workers since the kv store is not running distributed[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] All workers finished. Serializing model for prediction.[0m
    [34m#metrics {"StartTime": 1645773084.2363768, "EndTime": 1645773084.7342925, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"get_graph.time": {"sum": 496.9661235809326, "count": 1, "min": 496.9661235809326, "max": 496.9661235809326}}}[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] Number of GPUs being used: 0[0m
    [34m#metrics {"StartTime": 1645773084.7343607, "EndTime": 1645773084.9241216, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"finalize.time": {"sum": 686.8278980255127, "count": 1, "min": 686.8278980255127, "max": 686.8278980255127}}}[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] Serializing to /opt/ml/model/model_algo-1[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] Saved checkpoint to "/opt/ml/model/model_algo-1-0000.params"[0m
    [34m#metrics {"StartTime": 1645773084.9241924, "EndTime": 1645773084.9495714, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"model.serialize.time": {"sum": 25.338172912597656, "count": 1, "min": 25.338172912597656, "max": 25.338172912597656}}}[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] Successfully serialized the model for prediction.[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] #memory_usage::<batchbuffer> = 3.5982131958007812 mb[0m
    [34m[02/25/2022 07:11:24 INFO 139844299908736] Evaluating model accuracy on testset using 100 samples[0m
    [34m#metrics {"StartTime": 1645773084.9496238, "EndTime": 1645773084.9510815, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"model.bind.time": {"sum": 0.04029273986816406, "count": 1, "min": 0.04029273986816406, "max": 0.04029273986816406}}}[0m
    [34m#metrics {"StartTime": 1645773084.9511366, "EndTime": 1645773096.8902702, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"model.score.time": {"sum": 11939.229011535645, "count": 1, "min": 11939.229011535645, "max": 11939.229011535645}}}[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, RMSE): 0.3283896350838211[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, mean_absolute_QuantileLoss): 1602.989599926656[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, mean_wQuantileLoss): 0.03412824351371161[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, wQuantileLoss[0.1]): 0.020079625469613012[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, wQuantileLoss[0.2]): 0.03135376584196203[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, wQuantileLoss[0.3]): 0.038603218402800836[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, wQuantileLoss[0.4]): 0.04269301202355334[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, wQuantileLoss[0.5]): 0.04390008467244175[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, wQuantileLoss[0.6]): 0.042386337979702204[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, wQuantileLoss[0.7]): 0.03810158180564604[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, wQuantileLoss[0.8]): 0.030714157750925713[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #test_score (algo-1, wQuantileLoss[0.9]): 0.019322407676759613[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #quality_metric: host=algo-1, test RMSE <loss>=0.3283896350838211[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736] #quality_metric: host=algo-1, test mean_wQuantileLoss <loss>=0.03412824351371161[0m
    [34m#metrics {"StartTime": 1645773096.8903544, "EndTime": 1645773096.939641, "Dimensions": {"Algorithm": "AWS/DeepAR", "Host": "algo-1", "Operation": "training"}, "Metrics": {"setuptime": {"sum": 5.819797515869141, "count": 1, "min": 5.819797515869141, "max": 5.819797515869141}, "totaltime": {"sum": 48216.641664505005, "count": 1, "min": 48216.641664505005, "max": 48216.641664505005}}}[0m
    [34m[02/25/2022 07:11:36 INFO 139844299908736 integration.py:592] worker closed[0m
    
    2022-02-25 07:11:56 Uploading - Uploading generated training model
    2022-02-25 07:11:56 Completed - Training job completed
    Training seconds: 147
    Billable seconds: 147


### Create endpoint and predictor

Now that we have trained a model, we can use it to perform predictions by deploying it to an endpoint.

**Note:** remember to delete the endpoint after running this experiment. A cell at the very bottom of this notebook will do that: make sure you run it at the end.


```python
job_name = estimator.latest_training_job.name

# API 와 Endpoint의 차이 한 줄 정리

'''

지하철 최단 거리 경로를 제공하는 웹 서비스가 있다고 하자.

이 서비스를 이용하는 사용자는 출발역과 도착역을 설정하고 최단 경로를 찾는 버튼을 누른다.


이 때 최단 거리 경로를 구하는 서비스를 이용하기 위한 요청이 향하는 URI가, 엔드포인트이다.
'''

endpoint_name = sagemaker_session.endpoint_from_job(
    job_name=job_name,
    initial_instance_count=1,
    instance_type="ml.m5.2xlarge",
    image_uri=image_uri,
    role=role,
)
```

    ------!

To query the endpoint and perform predictions, we can define the following utility class: this allows making requests using `pandas.Series` objects rather than raw JSON strings.

- predictor 

Make real-time predictions against SageMaker endpoints with Python objects




```python
class DeepARPredictor(sagemaker.predictor.RealTimePredictor):
    def set_prediction_parameters(self, freq, prediction_length):
        """Set the time frequency and prediction length parameters. This method **must** be called
        before being able to use `predict`.

        Parameters:
        freq -- string indicating the time frequency
        prediction_length -- integer, number of predicted time points

        Return value: none.
        """
        self.freq = freq
        self.prediction_length = prediction_length

    def predict(
        self,
        ts,  # serise 객체
        cat=None, # default 값 설정 
        encoding="utf-8",
        num_samples=100, # 예측 샘플링 데이터 수 100개 설정 
        quantiles=["0.1", "0.5", "0.9"],  # 분위수 회귀(QUANTILE REGRESSION) / 우리가 흔히 하는 회귀분석은 주어진 상황(예측 변수가 주어졌을 때), 결과 변수의 평균을 예측한다. 이에 반해 Quantile regression은 예측 변수가 주어졌을 때, 결과 변수의 q-분위수를 예측한다.
        content_type="application/json", # 위에서 데이터를 json 형태로 바꾼 것을 적용함
    ):
        """Requests the prediction of for the time series listed in `ts`, each with the (optional)
        corresponding category listed in `cat`.

        Parameters:
        ts -- list of `pandas.Series` objects, the time series to predict
        cat -- list of integers (default: None)
        encoding -- string, encoding to use for the request (default: "utf-8")
        num_samples -- integer, number of samples to compute at prediction time (default: 100)
        quantiles -- list of strings specifying the quantiles to compute (default: ["0.1", "0.5", "0.9"])

        Return value: list of `pandas.DataFrame` objects, each containing the predictions
        """
        prediction_times = [x.index[-1] + pd.Timedelta(1, unit=self.freq) for x in ts] # Timedelta함수를 적용시켜 1시간 기준으로 예측 시간을 정할 수 있게 설정함 
        req = self.__encode_request(ts, cat, encoding, num_samples, quantiles) # encode 한 데이터를 request 해줌 , 밑에 __encode_request 함수 실행시켜줌
        # super함수를 사용해 sagemaker.predictor.RealTimePredictor 부모클래스에서 함수 가져와 사용함
        res = super(DeepARPredictor, self).predict(req, initial_args={"ContentType": content_type})
        return self.__decode_response(res, prediction_times, encoding) # decode 한 데이터를 response 해줌, 밑에 __decode_response 함수 실행시켜줌

    def __encode_request(self, ts, cat, encoding, num_samples, quantiles):
        instances = [series_to_obj(ts[k], cat[k] if cat else None) for k in range(len(ts))] # series 데이터를 json 객체로 변환 해주는 series_to_obj 함수 적용 
        configuration = {
            "num_samples": num_samples, # 위에서 설정한 num_samples=100, # 예측 샘플링 데이터 수 100개 설정 적용 시켜줌 
            "output_types": ["quantiles"], # 위에서 설정한 quantiles=["0.1", "0.5", "0.9"] 적용시킴 
            "quantiles": quantiles,
        }
        http_request_data = {"instances": instances, "configuration": configuration} # instances, configuration를 dictionary으로 만들어줌 
        return json.dumps(http_request_data).encode(encoding) # 사전형(dict형)을 JSON문자열로 정형화하여 출력 : json.dumps()

    def __decode_response(self, response, prediction_times, encoding):
        response_data = json.loads(response.decode(encoding)) # JSON문자열을 사전형으로 변환 : json.loads()

        list_of_df = []
        for k in range(len(prediction_times)): # 1시간 기준으로 예측할 시계열 데이터 적용
            prediction_index = pd.date_range(
                start=prediction_times[k], freq=self.freq, periods=self.prediction_length   # 위에서 설정한 변수 prediction_length = 48 시간 적용함
            )
            list_of_df.append(
                pd.DataFrame(
                    data=response_data["predictions"][k]["quantiles"], index=prediction_index
                )
            )
        return list_of_df
```


```python
predictor = DeepARPredictor(endpoint_name=endpoint_name, sagemaker_session=sagemaker_session)  # predictor 변수 설정함
predictor.set_prediction_parameters(freq, prediction_length)
```

    The class RealTimePredictor has been renamed in sagemaker>=2.
    See: https://sagemaker.readthedocs.io/en/stable/v2.html for details.


### Make predictions and plot results

Now we can use the previously created `predictor` object. For simplicity, we will predict only the first few time series used for training, and compare the results with the actual data we kept in the test set.

- time_series_training[:15] ,  time_series[:15] 으로 변경하면 그래프의 x축 값, y축 값이 안보임 , 따라서 5으로 설정한 것이 가장 이상적이다.


```python
list_of_df = predictor.predict(time_series_training[:5], content_type="application/json") # 훈련데이터 적용시킴
actual_data = time_series[:5] 
```


```python
for k in range(len(list_of_df)): #  훈련데이터 범위로 지정해줌
    plt.figure(figsize=(12, 6))
    actual_data[k][-prediction_length - context_length :].plot(label="target") # y축을 prediction_length + context_length 범위로 지정해줌
    p10 = list_of_df[k]["0.1"] # 위에서 설정한 quantiles 0.1으로 설정해줌
    p90 = list_of_df[k]["0.9"] # 위에서 설정한 quantiles 0.9으로 설정해줌
    '''
    matplotlib.pyplot.fill_between()
    The matplotlib.pyplot.fill_between() is used to fill area between two horizontal curves
    '''
    plt.fill_between(p10.index, p10, p90, color="y", alpha=0.5, label="80% confidence interval")
    list_of_df[k]["0.5"].plot(label="prediction median")
    plt.legend()
    plt.show()
```

![output_60_0]({{ site.gdrive_url_prefix }}12L1mPzl3kPtALe7mVBF5cGdUE3Xyu99l)



![output_60_1]({{ site.gdrive_url_prefix }}1MoFOKX_f2Vcuh87-uu3dBryh99JldFOZ)


![output_60_2]({{ site.gdrive_url_prefix }}1P2GpzemrmcH-XJBUHfmrupeandt8B92N)



![output_60_3]({{ site.gdrive_url_prefix }}1kxYLsmzwrRoSk6L_3s0uOEZqFBeuo7Df)


![output_60_4]({{ site.gdrive_url_prefix }}16eIdWzauI2nQMlcpHHigmc6wb96uIyyy)



### Delete endpoint


```python
sagemaker_session.delete_endpoint(endpoint_name) # 마지막에 endpoint 삭제해줘야함
```

