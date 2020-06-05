# Django의 app
`Django` 프로젝트에 대한 기본적인 부분을 알아봤으니, 이제 앱(app)을 만들어봅시다.  
프로젝트에다가 모든 걸 다 넣어도 됩니다. 다만, 하나의 프로젝트에 구분없이 모든 코드를 작성하면 유지보수하기에 어려워지겠죠? 보통 **기능**을 기준으로 앱을 생성합니다.  
하나의 프로젝트에는 여러 개의 앱이 포함될 수 있습니다.

## app 생성하기  
앱을 생성하기 위해 `manage.py`가 있는 디렉토리에서 아래 명령어를 입력해주세요.  
```python
$ python manage.py startapp [앱이름]
```
저는 `firstapp`이라는 이름으로 생성해 보았습니다.  

## app 등록
우선, 앱 생성을 하고 나서 프로젝트의 `settings.py` 파일에 들어가 주세요.
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'firstapp',
]
```
`INSTALLED_APPS` 부분에 새로 만든 앱의 이름을 추가해 주세요.  
작은따옴표(`''`)안에 작성해 주셔야 하고 끝에 쉼표(`,`)도 잊지말고 넣어주세요.  
또한, 앱 생성을 한 다음에 서버를 한번 껐다 켜 주는 것이 좋습니다.  

:bulb: 앱 이름만 넣어도 문제는 없지만, `Django` 공식 문서에서는 `'앱이름.apps.앱이름Config'`처럼 추가하도록 나와있습니다. 현재 예시에서는 `'firstapp.apps.FirstappConfig'`가 되겠습니다.  

## firstapp/ 디렉토리  
앱을 생성하면 아래와 같은 디렉토리가 생긴 것을 확인할 수 있습니다.  
```
firstproject/
├───manage.py
├───firstproject/
├───firstapp/
        migrations/
                __init__.py
        __init__.py
        admin.py
        apps.py
        models.py
        tests.py
        views.py
```

### migrations/ 디렉토리  
`Django`의 `MTV` 중 `M`, `Model` 부분과 관련되어 있습니다. 모델을 만들고 수정할때마다 자동으로 데이터베이스를 업데이트하는 파일들이 저장되는 디렉토리입니다.  
`migrations/` 디렉토리에 있는 `__init__.py` 파일 역시 이전에 설명드린 것처럼 해당 디렉토리를 `Python` 패키지로 인식하게 하는 용도의 빈 파일입니다.  

### __init__.py
우리가 생성한 `firstapp` 역시 `Python`이 `firstapp/` 디렉토리를 하나의 `Python` 패키지로 다루도록 지시합니다. 마찬가지로 빈 파일입니다.  

### admin.py  
```python
from django.contrib import admin

# Register your models here.
```
관리자 사이트의 설정 관련된 파일입니다. `Register your models here.`라는 주석에서 확인할 수 있듯, 생성한 모델을 관리자 사이트에 등록하는 데 사용합니다. 추후에 `Model`과 `admin`에 대해 설명할 때 더 자세하게 알려드릴게요.  

### apps.py  
```python
from django.apps import AppConfig


class FirstappConfig(AppConfig):
    name = 'firstapp'
```
앞서 [앱 등록](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/1st-semester/week-04#app-%EB%93%B1%EB%A1%9D)부분에서 알려드린 `FirstappConfig`가 이 부분입니다. 앱에 대한 기본 설정 정보를 담고 있는 파일이라고 생각하시면 됩니다.  

### models.py  
```python
from django.db import models

# Create your models here.
```
`Django`의 `MTV` 중 `M`, `Model`에 대한 정보를 정의하고 저장하는 파일입니다.  

### tests.py  
```python
from django.test import TestCase

# Create your tests here.
```
test case를 다루는 파일입니다.  

### views.py  
```python
from django.shortcuts import render

# Create your views here.
```
`Django`의 `MTV` 중 `V`, 앱에 대한 `Views`를 설정하는 파일입니다.  
