---
layout: single
title: "Django Inventory System Web Project 3 -재고관리시스템 파이썬 웹 프로젝트"
date: "2022-02-07 17:23:03 +0900"
last_modified_at: "2022-02-07 17:23:03 +0900"
---

# 재고관리시스템 파이썬 웹 프로젝트 3

## 1.1  터미널에 python manage.py startapp user 구문 입력후 user폴더 생성됨


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/30.png")
```



![output_2_0]({{ site.gdrive_url_prefix }}1wpBYE3MOFiIKKPaCgKUW4KIqIqI_TCzn)
    



## 1.2 config 폴더 settings.py 파일 수정

> INSTALLED_APPS = [] 에 'user.apps.UserConfig' 추가 해줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/31.png")
```


![output_4_0]({{ site.gdrive_url_prefix }}1Dr6kCzBNCBhZhzd8xr5aZH0YJvFU5sr3)




## 1.3 user 폴더 views.py 파일 수정


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/32.png")
```



![output_6_0]({{ site.gdrive_url_prefix }}1aS6GW1q0P-Vjk1llmWQzLqjAg-w2CjFt)
    




```python
from django.shortcuts import render

def register(request):
     return render(request, 'user/register.html')
```

## 1.4 templates 폴더 -> user폴더 register.html 생성

> 밑에 그림처럼 extends 추가해줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/32-2.png")
```




![output_9_0]({{ site.gdrive_url_prefix }}1wJeatttUTcTd_vmDqnmgtxpqooXBX-h4)
    



## 1.5 config폴더 urls.py 수정


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/33.png")
```



![output_11_0]({{ site.gdrive_url_prefix }}17bII4XuPIjKvmUdKmopNhrRfII6caAQJ)
    




```python
from django.contrib import admin
from django.urls import path, include
from user import views as user_view

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('dashboard.urls')), #dashboard 폴더에 urls 파일 경로 설정
    path('register/', user_view.register, name = 'user-register'),

]
```

## 1.6 Installing django-crispy-forms 

> django-crispy-forms 

>> 폼 레이아웃의 고급 기능들. 폼을 트위터의 부트스트랩 폼 엘리먼트와 스타일로 보여줍니다. django-floppyforms 와 함께 쓰기 좋아서 빈번하게 함께 쓰입니다. 장고 폼을 부트스트랩같은 스타일로 변환하여 보여줍니다. 상세 설정도 폼의 인스턴스 단위로 할 수 있습니다.

> 터미널에 pip install django-crispy-forms 또는 conda install django-crispy-forms으로 설치해 줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/34.png")
```




![output_15_0]({{ site.gdrive_url_prefix }}1-LOlurqFMR1uEQkbUSqqk7Gw4H9FlBLe)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/35.png")
```



![output_16_0]({{ site.gdrive_url_prefix }}1O7uF94ekb-1SqAxQOxaxcqSekW4f-TQG)
    



## 1.7 config 폴더 settings.py 파일 수정

> INSTALLED_APPS =[]에 'crispy_forms'을 설정해줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/35-2.png")
```


![output_18_0]({{ site.gdrive_url_prefix }}1JhBgTvWazSL4ZuLYe5PBM6Vwh7MPuc-O)
    




```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'dashboard.apps.DashboardConfig',
    'user.apps.UserConfig',
    'crispy_forms',
]
```

## 1.8 config 폴더 settings.py 파일 수정

> CRISPY_TEMPLATE_PACK = 'bootstrap4' 설정해줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/36.png")
```


![output_21_0]({{ site.gdrive_url_prefix }}12NU6y8lTlDXsYIy1tJSM0v6WZ3irxrhD)
    



## 1.9 templates 폴더 -> user폴더 register.html 수정

> crispy_forms_tags 을 html 파일 상단에 load 해 줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/37.png")
```



![output_23_0]({{ site.gdrive_url_prefix }}1v0yeNiDASfI0zBHLm4W9LXipSOFH-9nX)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/37-2.png")
```



![output_24_0]({{ site.gdrive_url_prefix }}1WoN7w4k_8xzK_VDrnkWdifCbE3TPfLLP)
    



## 1.10 user 폴더 views.py 파일 수정 

>  UserCreationForm 을 CreateUserForm()으로 바꿔줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/38.png")
```



![output_26_0]({{ site.gdrive_url_prefix }}1rEfsR4Q4D-z1maR0U6uV6rpkaXl8e8Tr)
    




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
            return redirect('dashboard-index') 
    else:
        form = CreateUserForm()
    context = {
        'form':form,


    }
    return render(request, 'user/register.html',context)
```

## 1.11 user 폴더 forms.py 파일 수정

> fields = '__all__' 추가 해 줍니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/39.png")
```



![output_29_0]({{ site.gdrive_url_prefix }}1NI8d_oiqsACJ42WJn1aaz-ANZi3wkvXx)
    




```python
from django import forms
from django.contrib.auth.models import User
from django.contrib.auth.forms import UserCreationForm

class CreateUserForm(UserCreationForm):
    email = forms.EmailField()

    class Meta:
        model = User
        fields = '__all__' 
        
```

## 1.12 다음과 같은 실행결과를 확인 할 수 있습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/40.png")
```




![output_32_0]({{ site.gdrive_url_prefix }}1UXyRuR0W2-CxKECjtN4uMlFhOaCihExw)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/40-2.png")
```



![output_33_0]({{ site.gdrive_url_prefix }}1FlB4eEXzw3cm9aKZ4VumwF144PoqtL8o)
    



## 1.13 fields = ['username', 'email', 'password1', 'password2'] 으로 변경 해 주었습니다.

> 다음과 같은 실행결과를 확인 할 수 있습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/41-2.png")
```



![output_35_0]({{ site.gdrive_url_prefix }}1u-s9QSwQY9sFX6qgkn1MYIqI_PbC_rAy)




```python
from django import forms
from django.contrib.auth.models import User
from django.contrib.auth.forms import UserCreationForm

class CreateUserForm(UserCreationForm):
    email = forms.EmailField()

    class Meta:
        model = User
        #fields = '__all__' 
        fields = ['username', 'email', 'password1', 'password2']
```


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/41.png")
```





![output_37_0]({{ site.gdrive_url_prefix }}1DW-c6QbRXqguwILt81QvNaoaaaMM1dtM)


