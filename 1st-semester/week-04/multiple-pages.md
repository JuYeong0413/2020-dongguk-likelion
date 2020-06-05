# 다른 페이지로 이동하기  
이제 다른 페이지로 이동하는 방법을 알아봅시다.  

## template  
우선 우리가 이동한 다음 화면에 보여줄 3개의 `html` 파일을 `firstapp`의 `main.html` 파일과 같은 디렉토리인 `firstapp/templates/firstapp/`에 만들어주세요.  
저는 `one.html`, `two.html`, `three.html` 이라는 세 개의 파일을 만들어서 각각 `<h1>1</h1>`, `<h1>2</h1>`, `<h1>3</h1>`이라고 작성했습니다.  

## views  
`html` 파일 작성이 끝났으니 이제 `views`로 넘어가야겠죠? `views.py`에 `one.html`, `two.html`, `three.html` 파일을 띄워주는 함수를 각각 작성해줍시다.  
```python
from django.shortcuts import render

# Create your views here.
def main(request):
    return render(request, 'firstapp/main.html')


def one(request):
    return render(request, 'firstapp/one.html')


def two(request):
    return render(request, 'firstapp/two.html')


def three(request):
    return render(request, 'firstapp/three.html')
```
:bulb: 함수와 함수 사이에는 2줄을 띄워주는 것이 좋습니다.  

## url  
`view` 작성도 완료되었으니, 마지막으로 어떤 `url`을 `view`와 연결할 것인지 `urls.py`에 작성해줍시다. `firstapp`의 `url`은 `firstapp/urls.py` 파일에서 관리하고 있었다는 걸 잊지 마세요.  
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.main),
    path('one/', views.one),
    path('two/', views.two),
    path('three/', views.three),
]
```
- `firstapp`의 `one/`으로 접속하면 `views`의 `one`이라는 함수를 실행해서 `one.html`을  
- `firstapp`의 `two/`로 접속하면 `views`의 `two`라는 함수를 실행해서 `two.html`을  
- `firstapp`의 `three/`로 접속하면 `views`의 `three`라는 함수를 실행해서 `three.html`을  

화면에 보여주는 `url`까지 모두 작성이 완료되었습니다.  

정확히 말하면, 우리는 `first/`로 시작하는 경로를 `firstapp`의 `urls.py`에서 관리하고 있기 때문에 `first/one/`, `first/two/`, `first/three/`로 접속해야 합니다.  

## 페이지 연결  
이제 `main.html` 파일에서 `one.html`, `two.html`, `three.html`으로 넘어갈 수 있도록 `a` 태그를 작성해 줍시다.  
```html
<h1>2020 Dongguk Likelion</h1>
<a href="/first/one">one</a>
<a href="/first/two">two</a>
<a href="/first/three">three</a>
```
앞서 말한 것처럼 정확한 `url`은 `first/`로 시작한다는 걸 잊지 마세요!  

이제 서버를 실행시켜서 한번 확인해 봅시다.  
`localhost:8000/first/`에 접속해보면 `one`, `two`, `three` 라고 적힌 링크가 보입니다. 클릭하면 각각 `1`, `2`, `3`이 보여지는 페이지로 이동하게 되는거죠.  

# Django스럽게 url 작성하기  
```html
<a href="/first/one">one</a>
<a href="/first/two">two</a>
<a href="/first/three">three</a>
```
위와 같은 방식은 링크가 길어지게 되면 가독성이 떨어지기 때문에 우리는 `name`을 부여해서 관리할 거에요.  

## url
`urls.py`에서 `path`에 `name`을 추가하면 됩니다.  
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.main),
    path('one/', views.one, name="page_one"),
    path('two/', views.two, name="page_two"),
    path('three/', views.three, name="page_three"),
]
```
- `one/`의 경로로 오는 `url`의 이름을 `page_one`  
- `two/`의 경로로 오는 `url`의 이름을 `page_two`  
- `three/`의 경로로 오는 `url`의 이름을 `page_three`  

라고 하겠다는 의미입니다.  

## template  
이제 `main.html`의 `a` 태그의 `href` 속성을 수정해줍시다.  
```html
<h1>2020 Dongguk Likelion</h1>
<a href="{% url 'page_one' %}">one</a>
<a href="{% url 'page_two' %}">two</a>
<a href="{% url 'page_three' %}">three</a>
```
여기서 사용된 `{% %}` 태그는 `Django`의 템플릿 태그(template tag) 입니다.  

각각 `page_one`이라는 이름의 `url`, `page_two`라는 이름의 `url`, `page_three`라는 이름의 `url`을 넣어주는 역할을 합니다. 개발자 도구를 열어서 확인해보면 아래와 같이 코드가 나오는 것을 확인할 수 있습니다.  
```html
<a href="/first/one/">one</a>
<a href="/first/two/">two</a>
<a href="/first/three/">three</a>
```
