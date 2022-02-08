---
layout: single
title: "Django Inventory System Web Project 4 -재고관리시스템 파이썬 웹 프로젝트"
date: "2022-02-08 18:31:22 +0900"
last_modified_at: "2022-02-08 18:31:12 +0900"
---

# Django Inventory System Web Project 4 -재고관리시스템 파이썬 웹 프로젝트

## 1.1 config 폴더 urls.py 파일 수정

> 로그인 페이지 ,로그아웃 페이지 경로 지정


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/42.png")
```




    

![output_3_0]({{ site.gdrive_url_prefix }}1ZELgsvWmcuOO9sudO-T2PmlfncwQTOzT)



```python
from re import template
from django.contrib import admin
from django.urls import path, include
from user import views as user_view
from django.contrib.auth import views as auth_views


urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('dashboard.urls')), #dashboard 폴더에 urls 파일 경로 설정
    path('register/', user_view.register, name = 'user-register'),
    path('login/',auth_views.LoginView.as_view(template_name='user/login.html'), name='user-login'),
    path('logout/',auth_views.LogoutView.as_view(template_name='user/logout.html'), name='user-logout'),

]
```

## templates 폴더 -> user 폴더 login.html 생성


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/43.png")
```


![output_6_0]({{ site.gdrive_url_prefix }}1RwK-5c8Cm_aqMup8GkYTSoI50lYSlM_r)
    



## 1.3 config 폴더 settings.py 파일 수정 

> LOGIN_REDIRECT_URL = 'dashboard-index' 추가 해줍니다.


> 로그인 페이지에서 Staff 또는 Superuser(최고 관리자)가 로그인 하면 dashboard/index.html 페이지으로 들어가게 해줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/44.png")
```


![output_8_0]({{ site.gdrive_url_prefix }}18xodpekB25U1P2kfTXdCv7RpayGt9hn1)




## 1.4 dashboard폴더 urls.py파일 수정

> dashboard-index 으로 설정하여 로그인하면 index.html 파일로 갈 수 있게 해줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/58.png")
```




![output_10_0]({{ site.gdrive_url_prefix }}1Z0B5E1iqH9KkVnwz9G-9v-crULs6XNlh)

    




```python
from unicodedata import name
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='dashboard-index'),
    path('staff/',views.staff, name='dashboard-staff'), # topnav.html에 url 연결시켜주기
    path('product/',views.product, name='dashboard-product'),
    path('order/',views.order, name='dashboard-order'),
]
```

> __로그인 후 index.html 파일로 가는 것을 확인 할 수 있습니다.__


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/46.png")
```





![output_13_0]({{ site.gdrive_url_prefix }}13HDQZRChn-sJ6ebf_csRcurtzrnyccfF)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/20220208_170341_1.png")
```



![output_14_0]({{ site.gdrive_url_prefix }}1_Jc87djdt2GzbDz5aBtShbqLXr3OOOKX)
    



- 아직 plot차트에 db를 적용시키기 전 캡쳐사진입니다. 임의로 데이터를 설정하여 프론트 화면만 구현해 봤습니다. 다음 보고서 문서에 db를 넣은 실행결과를 보여 드리겠습니다.

##  partials 폴더 nav.html파일에서 Logout url을 'user-logout' 경로를 밑에 그림과 같이 지정 해 줍니다. 상단 로그아웃 카테고리 버튼을 누르면 로그아웃되고 로그아웃 페이지로 갑니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/48.png")
```




![output_17_0]({{ site.gdrive_url_prefix }}1iynWeKVHE_6Eht7uEl3jDXE1-LdHFvwI)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/50.png")
```





![output_18_0]({{ site.gdrive_url_prefix }}164Ah7xUJ6UOTtpPEmaqpqhaqeR6arsMp)
    



## 1.6 register ,login href에 url을 밑에 그림과 같이 설정 


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/54.png")
```



![output_20_0]({{ site.gdrive_url_prefix }}1sevTLGaiVdwGtYb7c6CI0nCDEfyrFlnc)
    



## 1.7 register 페이지에서 회원가입하면 home으로 가는게 아니라 login 페이지로 갈 수 있게 설정함


> path 경로 수정하였습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/59.png")
```



![output_23_0]({{ site.gdrive_url_prefix }}1otuaeCIqlWIrlKMyUIo8TsMXdXCNHIBK)



* views파일에서 register 함수 반환 매개변수를 user-login으로 변경했습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/60.png")
```



![output_25_0]({{ site.gdrive_url_prefix }}19rlPDHqimXvXAgJT_7O-JpRvGcqZD9yr)

    




```python
from multiprocessing import context
from django.shortcuts import render,redirect
from django.contrib.auth.forms import UserCreationForm
from .forms import CreateUserForm


def register(request):
    if request.method =='POST':
        form = CreateUserForm(request.POST)
        if form.is_valid(): #유효하면 저장하라
            form.save()
            return redirect('user-login') 
    else:
        form = CreateUserForm()
    context = {
        'form':form,


    }
    return render(request, 'user/register.html',context)


```

> 회원가입 페이지에서 로그인페이지로 가는 것을 아래 그림에서 확인 할 수 있습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/61.png")
```




![output_28_0]({{ site.gdrive_url_prefix }}1NC7UoqAsqTIWw0PyDbj7l24yuz2e9ATx)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/62.png")
```




![output_29_0]({{ site.gdrive_url_prefix }}1fL1MMcR7_HQwmdVUWTwGmKRMgUzpEcAa)




## 1.8 login_required 데코레이터 함수 사용


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/63.png")
```



![output_31_0]({{ site.gdrive_url_prefix }}1grynUM7AfIhItvFSpasm-BGi7tklGmNN)

    



> __위와 결과가 동일하지만 더욱 간결한 코드로 작성했습니다.__ 


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/64.png")
```



![output_33_0]({{ site.gdrive_url_prefix }}188Jl69mSHrq-u-qH5B1yfgSFLBp43_si)
    




```python
from multiprocessing import context
from django.shortcuts import render

# HttpResponse: 응답에 대한 메타정보를 가지고 있는 객체
from django.http import HttpResponse
from django.contrib.auth.decorators import login_required 
from .models import Product

# 데코레이터(decorator)는 @ 붙여서 실행한다.
# 데커레이터는 다른 함수를 인수로 받는 콜러블(데커레이터된 함수)이다

@login_required
def index(request):
    #return HttpResponse('<h1> 메인 페이지 </h1>')
    return render(request, 'dashboard/index.html') # templates 폴더에서 index.html 불러옴 
    
    '''
    예시)
     return render(request, 'polls/index.html', context) 
     render() 함수는 request 객체를 첫번째 인수로 받고, 템플릿 이름을 두번째 인수로 받으며, context 사전형 객체를 세전째 선택적(optional) 인수로 받습니다. 인수로 지정된 context로 표현된 템플릿의 HttpResponse 객체가 반환됩니다.
  
    '''
@login_required
def staff(request):
    #return HttpResponse('관리자 페이지')
    return render(request, 'dashboard/staff.html')

@login_required
def product(request):
    items = Product.objects.all()
    context = {
        'items' : items,

    }
    return render(request, 'dashboard/product.html', context)    

@login_required
def order(request):
    return render(request, 'dashboard/order.html')        
```

> settings.py 파일에 LOGIN_URL = 'user-login' 변수를 설정해 주었습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/65.png")
```





![output_36_0]({{ site.gdrive_url_prefix }}1WlR-rbuNTXzTOYQkS56M2qSCklz3VWKo)
    


