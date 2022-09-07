---
layout: single
title: "자동주식투자프로그램 세팅방법"
date: "2022-09-07 21:07:00 +0900"
last_modified_at: "2022-09-07 21:07:00 +0900"
---

# 자동주식투자프로그램 프로젝트 환경설정

국내 증권사 API는 COM(Component Object Model)을 기반으로 서비스합니다. 그래서 윈도우라는 운영체제에서만 개발이 가능합니다. 여기에 추가로 파이썬 32-bit 만을 지원하기 때문에 파이썬 32-bit를 설치했습니다. 현재 사용중인 anaconda 64비트가 있지만 32비트이면서 파이썬 3.7 버전
을 anaconda 사이트에서 다운로드 받았습니다.

# vscode에서 32비트 anaconda 가상환경 사용하기

다음과 같이 vscode cmd에 입력해 줍니다.

1. conda create -n pyAI_32 python=3.7 

2. set CONDA_FORCE_32BIT=1 && conda create -n pyAI_32 python=3.7

3. conda activate C:\ProgramData\Miniconda3\envs\pyAI_32

![20220907_150406.png]({{ site.gdrive_url_prefix }}1dp3f6YfLuaNTkeewOn6HBGiK_zNOt1Ws)



또는, vscode에서 ctrl+shift+p 를 누른 후 python: Select Interpreter 선택 후 다음 그림과 같이 가상환경을 선택해줍니다.



![20220907_145757_1.png]({{ site.gdrive_url_prefix }}12HkW81sJTseBT5cQtG7QJj4ckmILZ6vQ)


vscode 터미널창을 command Prompt로 열어주고 라이브러리 설치를 위해
pip install -r requirements.txt 구문을 cmd에 입력하여 설치해줍니다.





