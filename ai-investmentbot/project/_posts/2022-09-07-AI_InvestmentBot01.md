---
layout: single
title: "자동주식투자프로그램 세팅방법"
date: "2022-09-07 21:07:00 +0900"
last_modified_at: "2022-09-07 21:07:00 +0900"
---

# 자동주식투자프로그램 세팅방법

국내 증권사 API는 COM(Component Object Model)을 기반으로 서비스합니다. 그래서 윈도우라는 운영체제에서만 개발이 가능합니다. 여기에 추가로 파이썬 32-bit 만을 지원하기 때문에 우리는 파이썬 32-bit를 설치했습니다. 그러나 요즘 인기있는 머신러닝이나 딥러닝 라이브러리인 텐서플로우나 파이토치등을 사용하려면 64-bit 파이썬을 사용해야합니다. 이런 경우에는 64-bit 아나콘다를 설치한 후 32-bit 아나콘다는 가상환경을 통해 생성할 수 있습니다.


현재 사용중인 anaconda 64비트가 있지만 32비트이면서 파이썬 3.7 버전으로 다운로드 받았다

#vscode에서 32비트 anaconda 가상환경 사용하기

다음과 같이 vscode cmd에 입력해 준다.

1. conda create -n pyAI_32 python=3.7 

2. set CONDA_FORCE_32BIT=1 && conda create -n pyAI_32 python=3.7

3. conda activate C:\ProgramData\Miniconda3\envs\pyAI_32

![20220907_150406.png]({{ site.gdrive_url_prefix }}1dp3f6YfLuaNTkeewOn6HBGiK_zNOt1Ws)



또는, vscode에서 ctrl+shift+p 를 누른 후 python: Select Interpreter 선택 후 다음 그림과 같이 가상환경을 선택해줍니다.



![20220907_145757_1.png]({{ site.gdrive_url_prefix }}12HkW81sJTseBT5cQtG7QJj4ckmILZ6vQ)


vscode 터미널창을 command Prompt로 열어주고 pip install -r requirements.txt 으로
설치해줌


## Bot 데이터베이스 종류


	   1. daily_craw : 종목 별 일 별 금융 거래 데이터를 저장하는 데이터 베이스
	   2. min_craw : 종목 별 분 별 금융 거래 데이터를 저장하는 데이터 베이스
	   3. daily_buy_list :
            1) 일자 별로 상장 된 모든 종목들의 금융 데이터를 저장
            2) 주식 종목 리스트 저장
            3) 테이블 종류
                stock_item_all : 코스피, 코스닥, 코넥스 모든 종목 저장하는 테이블
                stock_kospi : 코스피 종목 리스트
                stock_kosdaq : 코스닥 종목 리스트

        4. jackbot1_imi1 : 모의 투자 테이터베이스
            1) 테이블 종류
                all_item_db : 종목 거래 이력
                jango_data : 일별 수익률 정산 테이블
                possessed_item : 현재 보유 중인 종목 리스트 테이블
                setting_data : 봇 옵션 관리 및 DB 업데이트 일자 관리 테이블


* 초기 database 생성 쿼리문 (collector_v3.py를 실행시키면 자동으로 아래 db가 없을 시 생성 되도록 코드가 수정 되었습니다.)

    create database daily_craw ;

    create database min_craw ;
    
    create database daily_buy_list ;




