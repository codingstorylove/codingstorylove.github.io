---
layout: single
title: "Django Inventory System Web Project 5 -재고관리시스템 파이썬 웹 프로젝트"
date: "2022-02-08 20:05:22 +0900"
last_modified_at: "2022-02-08 20:05:22 +0900"
---

# Django Inventory System Web Project 5 -재고관리시스템 파이썬 웹 프로젝트

## 1.1 superuser 으로 등록된 계정으로 로그인 동영상


![output_4_0]({{ site.gdrive_url_prefix }}1NZBxB7TqGiVbE_7d25AvWtiIO4UecztT)

## 1.2 로그인 접근 권한 설정

> 최고관리자가 허락을 해야 로그인이 가능하도록 만들었습니다. 다음 그림과 같이 접근권한 구문을 추가했습니다.



```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/67.png")
```



![output_4_0]({{ site.gdrive_url_prefix }}1lqutvliFjCuNZB8qKvW_ozaMCDr3Y1O4)


​    



```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/68.png")
```



![output_5_0]({{ site.gdrive_url_prefix }}1YTnHvU7SIWKg31Ja1oiyM70LhsdKC9J6)




> 위의 그림은 최고관리자가 승인을 하지 못하면 로그인을 못하도록 만들었습니다 
> 승인을 받지 못한 유저는 다음과 같은 페이지로 이동하게 됩니다.

![66]({{ site.gdrive_url_prefix }}18knG7eai5NQQSve9IaMauVF8fFYXCu6J)



> 추후에 css를 수정할 계획입니다.

## 1.3 승인을 받은 유저 , 최고관리자는 다음 그림과 같이 관리자 페이지에 열람이 가능합니다

```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/69.png")
```


![output_10_0]({{ site.gdrive_url_prefix }}1YU8NXm7u-Wu1U1JyGHr5FJ5y4MI6Szbt)

​ 



> 위의 그림에서 STAFF STATUS 컬럼에서 최고관리자의 승인을 받지 못하면 X표시 빨간색으로 표시됩니다.

## 1.4 staff status 또는 Superuser status가 아래 그림과 같이 표시되어야만 관리자페이지를 열람할 수 있습니다.

```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/Django-Project/Django-Inventory-System-Web/data/70.png")
```




![output_13_0]({{ site.gdrive_url_prefix }}1uy8ND9xdUbW5uTuu-CMjkmbvIr13chl0)

