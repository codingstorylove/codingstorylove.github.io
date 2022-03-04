---
layout: single
title: "아마존닷컴처럼 Amazon Forecast로 시계열 예측하기 - 강지양 솔루션즈 아키텍트(AWS ), 강태욱 매니저(GSSHOP) 강의 공부 및 정리"
date: "2022-03-04 13:21:10 +0900"
last_modified_at: "2022-03-04 13:21:10 +0900"
---

# 아마존닷컴처럼 Amazon Forecast로 시계열 예측하기 - 강지양 솔루션즈 아키텍트(AWS ), 강태욱 매니저(GSSHOP) 강의 공부 및 정리 

## 사진 출처 : [https://www.youtube.com/watch?v=4MuVRFQVx8Y](https://www.youtube.com/watch?v=4MuVRFQVx8Y)

- Amazon Web Services Korea


## 아마존은 시계열 예측을 사용해서 상품수요예측, 재고관리예측,노동력 수요 예측, 재무지표 예측을 한다


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_162654_1.png")
```





![output_3_0]({{ site.gdrive_url_prefix }}1b0xR0bqNAKXxfcDD0JHOTxJAmMD7ndAp)




## 전통적인  통계 방식 모델인 ARIMA 모델(자동 회귀 통합 이동 평균)등은 정확한 예측이 어렵다.

1. 전통적인  통계 방식 모델으로는 날씨, 휴일, 이벤트( 세일, 쿠폰행사등)를 반영하기가 어렵다.

2. 새로운 상품이 나온다면 새 상품은 과거 데이터가 없기 때문에 전통적인 통계 방식 모델으로는 예측이 어렵다

3. 전통적인 통계 방식 모델으로 예측하기 어려워 딥러닝 방식 예측 알고리즘을 사용한다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_162654_2.png")
```




![output_5_0]({{ site.gdrive_url_prefix }}1btKP4uscphMogPpRSm8qxpFRq3s4WeWJ)
    



## 기존의 문제 접근 방식 

1. 전통적인 통계 모델 방식 ARIMA, ETS(지수평활)는 규칙적인 데이터로 되어 있어야지 예측이 가능하다. 하지만 불규칙한 패턴의 데이터는 전통적인 통계 모델 방식으로 예측이 어려워 딥러닝 방식을 사용해야한다.

2. 전통적인 통계 모델 방식 ARIMA, ETS는 외부요인(날씨,휴일,이벤트등), 제품속성(남성을 위한 제품인가 여성을 위한 제품인가), 브랜드 속성들을 전통적인 통계 모델 방식에 반영하기 어렵다. => 메타데이터 반영 문제

3. 머신러닝 모델인 Regression, XGBoost는 데이터의 연속성을 가진 속성들을 머신러닝 모델 방식에 반영하기 어렵다.

4. 마지막으로 딥러닝 모델들은 하이퍼파라미터 최적화 문제로 인해 매번 해야하는 어려움이 있다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_164010_1.png")
```



![output_7_0]({{ site.gdrive_url_prefix }}1452XZS9bRQaHjIGm8zRFwLuxULNNP5gv)

    



## 아마존닷컴의 기술로 어려웠던 예측 문제를 해결가능하다.

1. 아마존은 60억개 다양한 상품을 팔고 있다. 같은 제품 속성이라고 연관되고 공통된 특징들이 있을것이다. 예를 들어 TV라는 상품은 여러개 이지만 다양한 TV 상품 속성 안에 연관되고 공통된 속성을 찾는 것이 아마존닷컴의 기술이다. => 연관된 시계열 데이터들간의 관계 학습

2. 새로운 상품을 판매하려고 할때 이전에 판매된 새로운 상품과 비슷한 데이터로 예측 할 수 있는 기술을 가지고 있다. => 이전 데이터가 없는 새로운 제품의 수요 예측

3. 한번에 10억개의 데이터도 학습 할 수 있는 기술을 보유하고 있기 때문에 기존의 모델로는 학습 할 수 없었던 예측을 학습 할 수 있다. => 불규칙한 데이터도 예측 가능하다. ( 급격한 변화 예측 가능)


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_165131_1.png")
```



![output_9_0]({{ site.gdrive_url_prefix }}1rVP0xmP0nN59gffk9VsNfviQJHtJ4iKl)




## Amazon Forecast의 동작 방식

1. 종속 변수 (Y 값), 예측 하려는 값은 Target Time Series 이다. (판매량, 재고 ,서버수 등)

2. Related Time Series , 예측하려는 값과 연관된 데이터 (가격, 프로모션, 웹 조회수 등)

3. Item Metadata , 메타데이터 ( 카테고리, 브랜드, 색상 등 )



```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_170512_1.png")
```




![output_11_0]({{ site.gdrive_url_prefix }}1kaeW1PDcYYIiJj70G6YeAUMCVLnxGrYY)

    
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220303_171014_1.png")
```



![output_12_0]({{ site.gdrive_url_prefix }}1ip1eFGqOTy7_xtEJ2su7dnZi5Djf-3Sf)

    



> 최종적으로 맞춤형 예측 API 생성됨

## 아마존 확률 예측의 결과값 - p10,p50,p90,RMSE


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_121015_1.png")
```




![output_15_0]({{ site.gdrive_url_prefix }}17RkvGvELYK9pAGorjYMLooOg3iEsLi8k)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_121015_2.png")
```



![output_16_0]({{ site.gdrive_url_prefix }}1mq2Da8Y-IBvsyKOT4O9xdI3dLINPsDIP)




## GSSHOP 물류 서비스 아키텍처


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_121015_3.png")
```




![output_18_0]({{ site.gdrive_url_prefix }}1Eq0Zs57RrZ3TwK3_hj199N3-TVDkrwaT)
  



> 1. 애자일(agile) 이란? · 짧은주기의 개발단위를 반복하여 하나의 큰 프로젝트를 완성해 나가는 방식이다 

> 2. The waterfall model is a sequential software development process, in which progress is seen as flowing steadily downwards (like a waterfall) through the phases of requirements gathering, analysis, design, development, unit testing, integration testing, acceptance testing and then release:
    


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_123948_1.png")
```



![output_21_0]({{ site.gdrive_url_prefix }}1Tjk-8lbkcZcVJJEn2l8iOtvZuBxTU_pB)

    
    



- 사진 출처 : [https://www.inflectra.com/Methodologies/Waterfall.aspx](https://www.inflectra.com/Methodologies/Waterfall.aspx)

> 3. 하이브리드 클라우드란? hybrid cloud

- 출처 : [https://www.netapp.com/ko/hybrid-cloud/what-is-hybrid-cloud/](https://www.netapp.com/ko/hybrid-cloud/what-is-hybrid-cloud/)

- 하이브리드 클라우드란 혼합된 컴퓨팅, 스토리지 및 서비스 환경으로 다양한 플랫폼 간의 오케스트레이션을 포함합니다. 온프레미스 인프라, 프라이빗 클라우드 서비스, Amazon Web Services(AWS) 또는 Microsoft Azure와 같은 퍼블릭 클라우드로 구성됩니다. 데이터 센터에서 퍼블릭 클라우드, 온프레미스 컴퓨팅 및 프라이빗 클라우드를 함께 사용할 수 있다면 하이브리드 클라우드 인프라를 갖추고 있는 것입니다.

- 하이브리드 클라우드의 이점

클라우드 서비스로 비용을 절감할 수 있지만 클라우드 서비스의 주된 가치는 신속한 디지털 비즈니스 혁신을 지원하는 데 있습니다. 모든 기술 관리 조직은 IT 안건과 비즈니스 혁신 안건이라는 두 가지 안건에 따라 실행합니다. 일반적으로 IT 안건은 비용 절감에 중점을 둡니다. 그러나 디지털 비즈니스 혁신은 수익을 창출할 수 있는 투자에 초점을 맞춥니다.

하이브리드 클라우드의 주된 이점은 민첩성입니다. 디지털 비즈니스의 핵심 원칙은 신속한 적응과 방향 전환입니다. 엔터프라이즈는 퍼블릭 클라우드, 프라이빗 클라우드 및 온프레미스 리소스를 결합하여 경쟁 우위를 확보하는 데 필요한 민첩성을 확보할 수 있어야 합니다.

- 퍼블릭 클라우드: 장점과 단점

AWS 또는 Azure와 같은 서비스 공급자와 계약하는 경우 퍼블릭 클라우드를 사용하고 기본적으로 분산 데이터 센터 인프라의 일부분을 임대하게 됩니다. 퍼블릭 클라우드는 클라우드를 서비스형 인프라(IaaS)로 제공합니다. 규모의 경제, 탄력적인 확장성 및 완전 자동화된 실행이라는 이점을 제공하기 때문에 온프레미스 데이터 센터는 가격이나 효율성 측면에서 경쟁이 되지 않습니다.

장점:

확장성(확장 및 축소 둘 모두에 해당). 온디맨드 클라우드 리소스 덕분에 거의 제한이 없습니다.
자본 지출(CapEx) 절감. 자체 데이터 센터 장비를 모두 구입할 필요가 없습니다.
안정성. 서비스가 여러 데이터 센터에 분산되어 있기 때문에 안정적입니다.

단점:

데이터 보안에 대한 제어 감소. 데이터가 운영되는 위치와 적용되는 제한(지리적 제한 또는 기타 제한)에 대해 알 수 없습니다.
운영 비용(OpEx) 증가. 성능을 확장함에 따라 시간당 비용이 증가합니다.

- 프라이빗 클라우드: 장점과 단점

엔터프라이즈를 위한 전용 클라우드 인프라를 구축하는 경우에는 프라이빗 클라우드를 사용합니다. 이 경우 사용자가 직접 관리하든 타사 서비스를 사용하든, 데이터 센터에서 호스팅하든 외부에서 호스팅하든 관계없이 모두 프라이빗 클라우드입니다.

장점:
보안. 프라이빗 클라우드에서는 데이터와 애플리케이션이 방화벽 뒤에 유지되고 엔터프라이즈에서만 액세스할 수 있으므로 기밀 데이터를 처리하거나 저장하기에 더 적합합니다.
TCO의 잠재적 절감. 운영 비용을 줄여줍니다.
향상된 제어 및 사용자 지정. 엔터프라이즈의 기본 설정에 서버를 맞출 수 있습니다.
유연성. 중요하지 않은 데이터를 퍼블릭 클라우드로 이동하여 프라이빗 클라우드의 갑작스러운 수요 증가를 수용할 수 있습니다.

단점:
더 높은 비용. 초기 비용이 높고 구입한 장비에 대한 비용을 상환해야 합니다.
책임. 자체 데이터 센터, IT 하드웨어 및 엔터프라이즈 소프트웨어는 물론이고 자체 보안 및 규정 준수를 운영하고 유지 관리해야 합니다.
유연성 감소. 요구사항의 변화에 따라 IT 리소스를 확장하거나 축소할 때 유연성이 떨어집니다.
하이브리드 클라우드의 적합성
퍼블릭 클라우드에 적합하지 않는 사안도 있기 때문에 미래 지향적인 회사들은 클라우드 서비스의 하이브리드 조합을 선택합니다. 하이브리드 클라우드는 퍼블릭 클라우드와 프라이빗 클라우드의 이점을 모두 제공하며 데이터 센터의 기존 아키텍처를 활용합니다.

하이브리드 접근 방식을 사용하면 애플리케이션과 구성 요소를 서로 다른 경계(예: 클라우드와 온프레미스), 클라우드 인스턴스 간, 심지어 아키텍처(예: 기존 아키텍처와 현대적인 디지털 아키텍처) 간에 상호 운영할 수 있습니다. 이러한 수준의 배포 및 액세스 유연성은 데이터에도 필요합니다. 동적 디지털 환경에서 워크로드 또는 데이터 세트를 처리하는 경우 변화하는 요구사항에 대응하여 워크로드 또는 데이터 세트의 이동에 대한 계획을 세워야 합니다. 시간이 지나면 애플리케이션 또는 데이터의 현재 위치가 가장 좋은 위치가 아닐 수도 있습니다.

하이브리드 클라우드 아키텍처에는 다음과 같은 특징이 있습니다.

온프레미스 데이터 센터, 프라이빗 및 퍼블릭 클라우드 리소스와 워크로드는 분리된 상태로 유지되지만 공통의 데이터 관리에 연결됩니다.
기존 아키텍처에서 비즈니스 크리티컬 애플리케이션을 실행하거나 퍼블릭 클라우드에 적합하지 않은 기밀 데이터를 포함하는 기존 시스템을 연결할 수 있습니다.
하이브리드 클라우드 인프라는 소프트웨어 정의 접근 방식을 사용하여 IT 리소스 전반에 공통된 데이터 서비스 세트를 제공하는 Data Fabric을 통해 지원됩니다.

- 하이브리드 클라우드 시나리오

동적 워크로드 또는 자주 변경되는 워크로드. 동적 워크로드에는 확장이 간편한 퍼블릭 클라우드를 사용하고 휘발성이 적거나 덜 중요한 워크로드는 프라이빗 클라우드 또는 온프레미스 데이터 센터에 유지합니다.
중요한 워크로드를 중요도가 낮은 워크로드와 분리. 중요한 금융 또는 고객 정보를 프라이빗 클라우드에 저장하고 퍼블릭 클라우드를 사용하여 나머지 엔터프라이즈 애플리케이션을 실행할 수 있습니다.
빅데이터 처리. 빅데이터를 거의 일정한 볼륨으로 지속적으로 처리할 가능성은 낮습니다. 대신 높은 확장성을 자랑하는 퍼블릭 클라우드 리소스를 사용하여 빅데이터 분석을 실행하는 동시에 프라이빗 클라우드를 사용하여 데이터 보안을 보장하고 중요한 빅데이터를 방화벽으로 보호할 수 있습니다.
엔터프라이즈의 상황에 맞춰 클라우드로 점진적으로 이전. 워크로드의 일부를 퍼블릭 클라우드 또는 소규모 프라이빗 클라우드에 배치합니다. 엔터프라이즈에 적합한 솔루션을 확인하고 퍼블릭 클라우드, 프라이빗 클라우드 또는 혼합 클라우드로 필요에 따라 클라우드 환경을 확장할 수 있습니다.
임시 처리 용량이 필요한 경우. 하이브리드 클라우드를 사용하면 자체 데이터 센터의 IT 인프라를 사용하는 것보다 저렴한 비용으로 단기 프로젝트에 퍼블릭 클라우드 리소스를 할당할 수 있습니다. 이렇게 하면 일시적으로만 필요한 장비에 과도하게 투자하지 않아도 됩니다.
미래를 위한 유연성. 현재의 요구사항을 제대로 충족하더라도 다음 달 또는 내년에는 요구사항이 어떻게 바뀔지 알 수 없습니다. 하이브리드 클라우드 접근 방식을 사용하면 퍼블릭 클라우드, 프라이빗 클라우드 또는 온프레미스 리소스 중에서 실제 데이터 관리 요구사항을 처리하기에 가장 적합한 리소스를 사용할 수 있습니다.
일석이조. 퍼블릭 클라우드 솔루션이나 프라이빗 클라우드 솔루션만으로 분명한 요구사항을 충족할 수 있는 것이 아니라면 옵션에 제한을 두지 않아야 합니다. 하이브리드 클라우드 접근 방식을 선택하면 두 가지 환경의 장점을 동시에 활용할 수 있습니다.

> 4. AWS 클라우드에서 Microsoft 애플리케이션을 실행

고객은 다른 클라우드 공급자보다 더 오랫동안, 12년 넘게 AWS에서 Microsoft 워크로드를 실행해왔습니다. 고객이 AWS를 선택하는 이유는, AWS가 클라우드에서 Microsoft 애플리케이션을 실행해온 가장 많은 경험을 갖추었고, 더 뛰어난 성능과 안정성, 우수한 보안 및 자격 증명 서비스, 보다 다양한 마이그레이션 지원, 가장 광범위하고 심층적인 기능, 보다 저렴한 총 소유 비용, 유연한 라이선스 옵션과 같이 다양한 혜택을 보장하는 Windows Server 및 SQL Server를 위한 최고의 플랫폼을 제공하기 때문입니다. AWS는 Active Directory, .NET, Microsoft SQL Server, Windows Desktop-as-a-Service 및 모든 지원되는 Windows Server 버전 등 Windows 애플리케이션을 구축 및 실행하는 데 필요한 모든 것을 지원합니다. AWS의 입증된 전문 지식을 통해 Windows 워크로드에 대한 리프트 앤 시프트, 리팩터링 또는 현대화까지도 손쉽게 이행할 수 있도록 지원합니다.

## RETAIL DOMAIN 


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_121015_4.png")
```



![output_26_0]({{ site.gdrive_url_prefix }}1U5vUXa_P6_5wwCBlJwDezojfLLLrkkbn)

    
    



## 데이터 준비


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_121015_5.png")
```



![output_28_0]({{ site.gdrive_url_prefix }}1vI61yFQzXZigQyVhH-u7ola2bBCs9JDo)



## RELATED_TIME_SERIES DATA 구체적인 데이터


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_121015_6.png")
```



![output_30_0]({{ site.gdrive_url_prefix }}1uWFi04nV7Ua1L_0eGD6SVcYP9z_z_UFK)
   
    



## Recipes - 알고리즘 선택 


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_121015_7.png")
```



![output_32_0]({{ site.gdrive_url_prefix }}1XfE6sLKpNlQubR9M5IAHDuWR4HI6w35x)

    



> GSSHOP은 MQRNN 알고리즘을 사용하여 예측을 하였다.



## 실행결과 - 왼쪽 실제 데이터, 오른쪽 예측 데이터


```python

from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_123124_1.png")
```



![output_35_0]({{ site.gdrive_url_prefix }}1ZzVBU1a_elaAuQ5uqaK4tdMZdeDugOEw)

    



## PoC 시스템


```python

from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_123124_2.png")
```



![output_37_0]({{ site.gdrive_url_prefix }}18fcqlf15QMsbNYRbcTNn9kUO1CfjnGDy)

    



> SKD란?

Software Development Kit의 약자로 AWS를 프로그래밍적으로 제어하기 편리하도록 제공되는 라이브러리들을 의미합니다. 언어별로 다양한 라이브러리를 제공하고 있기 때문에 자신에게 맞는 라이브러리를 선택해서 사용하시면 됩니다. 

> Python용 AWS SDK(Boto3)

Boto3 is the Amazon Web Services (AWS) Software Development Kit (SDK) for Python, which allows Python developers to write software that makes use of services like Amazon S3 and Amazon EC2. You can find the latest, most up to date, documentation at our doc site, including a list of services that are supported.

- 출처 : [https://github.com/boto/boto3](https://github.com/boto/boto3)   <- boto3 라이브러리 참고

## POC 결과


```python

from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_123124_3.png")
```




![output_41_0]({{ site.gdrive_url_prefix }}1WwqCsy6ZVYniHn1XxpCh_ZJjcJG35tJ8)

    



## POC 히스토그램 결과


```python

from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_123124_4.png")
```


    

![output_43_0]({{ site.gdrive_url_prefix }}1FEW20nxmkY9XSsAvOKnLCiLcf5J7Gmg7)




```python

from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_123124_5.png")
```



    


![output_44_0]({{ site.gdrive_url_prefix }}1n8Zxd71c7T2jXo0X5QVoq5WTL5m43n-v)



## 알고리즘 선택 보다 더 중요한 데이터의 양


```python

from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/AWS/data/20220304_123124_6.png")
```




    
![output_46_0]({{ site.gdrive_url_prefix }}1hEHE_BoDIbVdnfcMy6SvlvgBBd9tA0YL)



