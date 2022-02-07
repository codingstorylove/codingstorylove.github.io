---
layout: single
title: "Django Inventory System Web Project 2 -재고관리시스템 파이썬 웹 프로젝트"
date: "2022-02-07 14:46:03 +0900"
last_modified_at: "2022-02-07 14:46:03 +0900"
---

# 재고관리시스템 파이썬 웹 프로젝트 2

## 1.1 dashboard 폴더 views.py 파일 수정

* Render




```python
render(request(필수), template_name(필수), 
      context=None, content_type=None, 
      status=None, using=None)

```

1. render는 httpRespose 객체를 반환하는 함수로 template을 context와 엮어 httpResponse로 쉽게 반환해 주는 함수입니다.

2. template_name에는 불러오고 싶은 템플릿명이 들어갑니다.

3. context에는 View에서 사용하던 변수(dictionary 자료형)를 html 템플릿에서 전달하는 역할을 하고, key 값이 템플릿에서 사용할 변수이름, value값이 파이썬 변수가 됩니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/10.png")
```


![output_5_0]({{ site.gdrive_url_prefix }}1y7_Hgv-fmxTpvr4hHhUuvSZVPtD5J8-u)
    




```python
from django.shortcuts import render

# HttpResponse: 응답에 대한 메타정보를 가지고 있는 객체
from django.http import HttpResponse

def index(request):
    #return HttpResponse('<h1> 메인 페이지 </h1>')
    return render(request, '') # templates 폴더에서 index.html 불러옴 
    
    '''
    예시)
     return render(request, 'polls/index.html', context) 
     render() 함수는 request 객체를 첫번째 인수로 받고, 템플릿 이름을 두번째 인수로 받으며, context 사전형 객체를 세전째 선택적(optional) 인수로 받습니다. 인수로 지정된 context로 표현된 템플릿의 HttpResponse 객체가 반환됩니다.
    '''
def staff(request):
    #return HttpResponse('관리자 페이지')
    return render(request, '')

```

## 1.2 config 폴더 settings.py 파일 수정

templates 폴더 만들어 주고 setting.py 파일에서 'DIRS' 부분을 수정해주었습니다.

그 이유는 templates 폴더에 있는 html 파일들을 웹페이지에 적용시켜 프론트 화면을 보여주기 위해서 입니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/11.png")
```


![output_9_0]({{ site.gdrive_url_prefix }}11aSi1Ra8FNW5WNVLm5HQKG9NhWRfH6Jq)





```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

## 1.3 asert 폴더 생성 

터미널에 python manage.py collectstatic 입력 하면 asert 폴더 자동 생성됩니다.



> python manage.py collectstatic

>> Django 프로젝트를 Deploy할 때 흩어져 있는 Static 파일들을 모아 특정 디렉토리로 옮기는 작업을 할 수 있는데, 이 작업은 위해 "./manage.py collectstatic" 명령을 사용합니다. 
즉, collectstatic 명령은 Django 프로젝트와 각 Django App 안에 있는 Static 파일들을 settings.py 파일 안에 정의되어 있는 STATIC_ROOT 디렉토리로 옮기는 작업을 수행합니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/12.png")
```



![output_13_0]({{ site.gdrive_url_prefix }}1TPnLH4v0ilvFvcQPjzBYz5krwnFW9s_p)
    



## 1.4 static 폴더 style.css 생성


```CSS
body{
    background: rgb(81, 138, 123);
}

.my-card:hover{
    transform: scale(1.1);
    transition: 0.2s ease-in-out;
}
```

## 1.5 templates 폴더 -> partials 폴더 base.html 수정


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/13.png")
```



![output_17_0]({{ site.gdrive_url_prefix }}1GCSiVgTFftJGH14w-6ic2x7oZdTtTUIa)






## 1.6 base.html에 맨위에 load static 경로 추가


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/14.png")
```


![output_20_0]({{ site.gdrive_url_prefix }}1QlU28oRnIyTWIeSIUellTO8z7L1JUlvA)




## 1.7 Product db 생성

> models.py 파일에 Product 클래스 만들고 name, category, quantity (db 변수) 설정

> 터미널에 python manage.py makemigrations 입력하고 python manage.py migrate 입력하면 0001_initial.py 파일 생성됩니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/15.png")
```



![output_22_0]({{ site.gdrive_url_prefix }}16ctA-ZSwd1Gd_2UwoXVb6Fc6_6FjXSXt)




```python
from calendar import c
from sre_constants import CATEGORY
from unicodedata import category, name
from django.db import models

CATEGORY = (
    ('Stationary','Stationary'),
    ('Electronics','Electronics'),
    ('Food','Food'),

)


class Product(models.Model):
    name = models.CharField(max_length=100, null=True)
    category = models.CharField(max_length=20, choices=CATEGORY, null=True)
    quantity = models.PositiveIntegerField(null=True)
```


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/17.png")
```




![output_24_0]({{ site.gdrive_url_prefix }}14PqxfWEOVhWdeeWsjaUGrNQF1tul0Q9Y)



## 1.8 0001.initial.py 파일 생성된 것을 확인 할 수 있습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/16.png")
```




![output_26_0]({{ site.gdrive_url_prefix }}1-KvoLs3T_8nCNTpAY_Sfbqk2n304_Iz3)

    




```python
from django.db import migrations, models


class Migration(migrations.Migration):

    initial = True

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='Product',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('name', models.CharField(max_length=100, null=True)),
                ('category', models.CharField(choices=[('Stationary', 'Stationary'), ('Electronics', 'Electronics'), ('Food', 'Food')], max_length=20, null=True)),
                ('quantity', models.PositiveIntegerField(null=True)),
            ],
        ),
    ]
```

## 1.9 터미널에 python manage.py createsuperuser 입력하면 user db 생성됩니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/18.png")
```



![output_29_0]({{ site.gdrive_url_prefix }}121l-pS3HyhnEMBzekHR7qGcSD8Oyc6ZR)




## 1.10 Superuser 생성된 것을 아래 그림에서 확인 할 수 있습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/19.png")
```





![output_31_0]({{ site.gdrive_url_prefix }}1Q3hk0jma9vCUx3JQnLS4gC2jbSaMZ1zU)
    



## 1.11 Product db 생성된 것을 아래 그림에서 확인 할 수 있습니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/20.png")
```





![output_33_0]({{ site.gdrive_url_prefix }}1V9qdzxJmtExCt11mC3A2kX8mGvV6LEmx)
    



> 위에 페이지를 이용해서 전기주전자를 등록했습니다. ** Product db 성공적으로 생성되었습니다. **


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/25.png")
```



![output_35_0]({{ site.gdrive_url_prefix }}1F1aVov_Ss4EpFBl9mai5rEzMIl4Y8h4a)
    



> 이 사진은 admin.py 파일에서 작성된 페이지입니다.

> list_filter = ['category'] 설정해주어서 위의 그림 화살표 폼이 만들어 졌습니다.


### admin.py 파일


```python
from csv import list_dialects
from django.contrib import admin
from .models import Product, Order
from django.contrib.auth.models import Group

# 맨위 상단 이름 변경
admin.site.site_header = '재고 관리 시스템 관리자 페이지'


class ProductAdmin(admin.ModelAdmin):
    list_display = ('name','category','quantity')
    list_filter = ['category']



admin.site.register(Product, ProductAdmin)
# admin.site.unregister(Group)  # Group 기능 제거할수 있는 방법

```

## 1.12 dashboard 폴더 models.py 파일에 Order 클래스 추가


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/26.png")
```





![output_40_0]({{ site.gdrive_url_prefix }}1nLvgJCvDC0ijZWQ_UT3MyazRihiLZI9Z)





```python
from calendar import c
from sre_constants import CATEGORY
from tabnanny import verbose
from unicodedata import category, name
from django.db import models
from django.contrib.auth.models import User

CATEGORY = (
    ('Stationary','Stationary'),
    ('Electronics','Electronics'),
    ('Food','Food'),

)


class Product(models.Model):
    name = models.CharField(max_length=100, null=True)
    category = models.CharField(max_length=20, choices=CATEGORY, null=True)
    quantity = models.PositiveIntegerField(null=True)



    def __str__(self):   # Product 제품 이름 보이게 설정
        return f'{self.name}'

class Order(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE, null=True)
    staff = models.ForeignKey(User,models.CASCADE, null=True)
    order_quantity = models.PositiveIntegerField(null=True )
    date = models.DateTimeField(auto_now_add=True)

# str : 인스턴스 출력 형식 함수
    def __str__(self):
        return f'{self.product} ordered by {self.staff.username}'
```

## 1.13 Order db 생성

> 터미널에 python manage.py makemigrations 입력하고 python manage.py migrate 입력하면 0002_order.py 파일 생성됩니다.


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/27.png")
```



![output_43_0]({{ site.gdrive_url_prefix }}1O8eJryMnX7cRhdr6GtsQ0RmNviApbJhy)

    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/28.png")
```




![output_44_0]({{ site.gdrive_url_prefix }}1PQixR78IT0AGSjEyCrdqaHDa7Kh4ArQn)
    



### Order 페이지 생성 

> order 페이지에 다음 그림과 같이 입력하면 order db에 저장된 것을 아래 그림에서 확인 할 수 있습니다.

> admin.py 파일에 import Order 추가 해주고, admin.site.register(Order) 추가 해 줍니다. 


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/20220207_144001_1.png")
```




![output_46_0]({{ site.gdrive_url_prefix }}1HAtOT_in1zh9sj9HjEffUutyan9lvsXJ)
    




```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/29-1.png")
```


![output_47_0]({{ site.gdrive_url_prefix }}1LSEciNSRpaIdjqcc90TZ_gAoWrrnD2Bo)



```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/29.png")
```



![output_48_0]({{ site.gdrive_url_prefix }}1jAAr3HKOpqwYgdpcRvWBAdChlBOawDIY)


