# 앱의 template 구조
앞서 [HTML 페이지 띄우기](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/1st-semester/week-04/04-page.md#template)에서  
> 앱 디렉토리인 `firstapp/` 안에 `templates` 폴더를 만들어줍시다. 그리고 `templates` 폴더 안에 앱 이름과 동일한 `firstapp`이라는 폴더를 만들어줍니다. 우리가 앞으로 앱에서 작성할 `html` 파일은 모두 `앱/templates/앱이름/` 디렉토리에 작성할 거에요.  

라고 말씀드렸는데요, `templates/` 디렉토리 안에 또다시 `firstapp/`이라는 폴더를 만들어서 `html` 파일을 작성하는 이유에 대한 설명을 지금 해드리려고 합니다.  

`Django` 프로젝트는 코드를 위에서부터 아래로 읽어갑니다. 그렇기 때문에 코드 순서에 영향을 받습니다.  

새로운 프로젝트로 예시를 들어보겠습니다.  
```bash
$ django-admin startproject templateproject
```
그리고 `templateproject/` 디렉토리로 이동한 다음 새로운 앱을 만들어 볼게요.  
```bash
$ cd templateproject/
$ python manage.py startapp first
```
새로운 앱을 생성했으니 `settings.py` 파일에 가서 `INSTALLED_APPS`에 추가해줍시다.  
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'first',
]
```
`first/` 디렉토리에 `html`파일을 모아둘 `templates/` 폴더를 만들어줍시다.  
우선 `hello.html` 파일을 만들어서 아래와 같이 작성해줄게요.  
```html
<h1>Hello first app</h1>
```
그리고 `main.html` 파일을 만들어서 다른 페이지로 넘어갈 수 있는 `a` 태그를 작성해줄게요.  
```html
<a href="{% url 'hello_page' %}">이동하기</a>
```

`views.py`에서 함수도 작성해 줍시다.  
```python
from django.shortcuts import render

# Create your views here.
def main(request):
    return render(request, 'main.html')


def hello(request):
    return render(request, 'hello.html')
```
`first` 앱의 `url`을 따로 관리하기 위해 `first/` 디렉토리에 `urls.py` 파일을 만들어줄게요.  
그리고 `path`를 작성해줍니다.  
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.main),
    path('hello/', views.hello, name="hello_page"),
]
```

이제 두 번째 앱을 만들어줍시다.  
```bash
$ python manage.py startapp second
```
`second` 앱 역시 `settings.py`에 추가해줍니다.  
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'first',
    'second',
]
```
마찬가지로 `second/` 디렉토리에 `html`파일을 모아둘 `templates/` 폴더를 만들어줍시다.  
같은 이름의 `hello.html` 파일을 만들어서 아래와 같이 `first` 앱의 `hello.html`과 구분할 수 있게 작성해줄게요.  
```html
<h1>Hello second app</h1>
```

`views.py`에서 함수도 작성해 줍시다.  
```python
from django.shortcuts import render

# Create your views here.
def hello(request):
    return render(request, 'hello.html')
```
`second` 앱의 `url`도 따로 관리하기 위해 `second/` 디렉토리에 `urls.py` 파일을 만들어줄게요.  
그리고 `path`를 작성해줍니다.  
```python
from django.urls import path
from . import views

urlpatterns = [
    path('hello/', views.hello, name="hello_page"),
]
```

프로젝트의 `urls.py`에 `first`와 `second`의 `urls`를 include 해주세요.  
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('firstapp/', include('first.urls')),
    path('secondapp/', include('second.urls')),
]
```

서버를 실행시킨 다음, `http://localhost:8000/firstapp/`으로 들어가면 이동하기 링크가 보입니다.  
링크를 클릭해보면 `Hello first app` 페이지가 보여집니다.  

한번 프로젝트의 `settings.py` 파일에서 `first`와 `second` 앱의 자리를 바꿔볼게요.  
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'second',
    'first',
]
```
새로고침을 하고 다시 `http://localhost:8000/firstapp/`에서 이동하기 링크를 클릭해보면 이번에는 `Hello second app` 페이지가 보여집니다.  

따라서 우리가 `Django` 프로젝트에게 어떤 페이지를 띄워줄지 정확하게 알려주기 위해  
1. `templates/` 디렉토리 안에 앱 이름과 동일한 폴더를 하나 더 만들어서 `html` 파일 작성  
2. `app_name`으로 앱 이름을 설정  

하는 일을 해 주셔야 합니다.  

