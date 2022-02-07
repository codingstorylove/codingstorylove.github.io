---
layout: single
title: "Django Inventory System Web Project 1 -재고관리시스템 파이썬 웹 프로젝트"
date: "2022-02-05 18:09:03 +0900"
last_modified_at: "2022-02-05 18:09:04 +0900"
---


# 재고관리시스템 파이썬 웹 프로젝트 

터미널에 pip install django 으로 설치해줍니다.

django version 3.1.7 으로 설치해 주었습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/2.png")
```



![output_3_0]({{ site.gdrive_url_prefix }}1r5WnMJM8QNMV6VohzEQq5qPkwtaVMzhy)
    



다음과 같이 c드라이브에 폴더를 만들어주고 django-admin startproject config . 구문을 터미널에 입력해서 django project을 다음과 같이 만들어 주었습니다.

## 1.1 Dashboard를 위한 Django앱 작성

터미널에 python manage.py startapp dashboard을 입력해서 dashboard 폴더 만들어 주었습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/3.png")
```




      
![output_6_0]({{ site.gdrive_url_prefix }}1u44zgMpYYCJfTzXePcD4FrjFifgGoGXC)



## 1.2 settings.py 파일 수정 

INSTALLED_APPS 변수에 'dashboard.apps.DashboardConfig' 추가해주었습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/4.png")
```




![output_8_0]({{ site.gdrive_url_prefix }}1_WpvWvm3kOo8UVhRhnLAU3gNR6LWBesA)



## 1.3 views.py 파일 수정


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/5.png")
```




![output_10_0]({{ site.gdrive_url_prefix }}13GDmNPJ2Keh9TNhHDnFLqe7Oy3yewhcx)




```python
from django.shortcuts import render

# HttpResponse: 응답에 대한 메타정보를 가지고 있는 객체
from django.http import HttpResponse

def index(request):
    return HttpResponse('메인 페이지')
```

## 1.4 dashboard 폴더 urls.py 파일 수정


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/6.png")
```



![output_13_0]({{ site.gdrive_url_prefix }}1y3hvfoQ-inIK-nFCEMI8Rqni1qLElYml)
    




```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index')
]
```

## 1.5 config폴더 urls.py 파일 수정


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/7.png")
```



![output_16_0]({{ site.gdrive_url_prefix }}1DB39PH9_3clODJeI_6P-MiE-5X0t3OiF)
    




```python
"""config URL Configuration
The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/3.1/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('dashboard.urls')), #dashboard 폴더에 urls 파일 경로 설정
]

```

## 1.6 실행결과


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/8.png")
```



![output_19_0]({{ site.gdrive_url_prefix }}1M6lZ2X7q5hIwAVHxKrEFrnZkA11-EB0i)



## 1.7 dashboard 폴더 views.py 파일 수정

관리자 페이지 HttpResponse 함수 만들어줍니다.


> HttpResponse

>> html 파일, 이미지 등 다양한 응답을 해줄 수 있습니다. 그리고 FBV 기준으로 HttpResponse 가 없다면 오류를 발생합니다. 하나의 함수는 최소 하나의 HttpResponse 을 반환해야합니다. 이는 views 에서 검사하는게 아니라 middleware 에서 검사를 하게 됩니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/9.png")
```




![output_21_0]({{ site.gdrive_url_prefix }}1FNOzQLASUITXXgTuVcPZVhr3KvH5xTZd)




```python
from django.shortcuts import render

# HttpResponse: 응답에 대한 메타정보를 가지고 있는 객체
from django.http import HttpResponse

def index(request):
    return HttpResponse('메인 페이지')
    
    '''
    예시)
     return render(request, 'polls/index.html', context) 
     render() 함수는 request 객체를 첫번째 인수로 받고, 템플릿 이름을 두번째 인수로 받으며, context 사전형 객체를 세전째 선택적(optional) 인수로 받습니다. 인수로 지정된 context로 표현된 템플릿의 HttpResponse 객체가 반환됩니다.
    '''
def staff(request):
    return HttpResponse('관리자 페이지')
```
