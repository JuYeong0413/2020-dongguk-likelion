# app_name 설정하기  
앞서 `Django` 프로젝트에서 기능별로 나누어 앱을 생성한다고 말씀드렸습니다. 이번에는 각각의 앱에 대해 `app_name`을 설정하는 것에 대해 알려드리려고 해요.  

## url  
`firstapp`의 `urls.py`에 들어가주세요.  
```python
from django.urls import path
from . import views

app_name = 'firstapp'
urlpatterns = [
    path('', views.main),
    path('one/', views.one, name="page_one"),
    path('two/', views.two, name="page_two"),
    path('three/', views.three, name="page_three"),
]
```
`urlpatterns`를 작성한 곳 위에 `app_name = 'firstapp'`을 추가해 주세요.  
`app_name`이 바로 해당 앱의 이름을 나타냅니다.  

이 상태에서 서버를 실행하면 에러가 발생합니다. 우리가 앱 이름을 설정해주었으니 `template`도 앱 이름을 넣어주어야 합니다.  

## template  
`main.html` 파일에서  
```html
<h1>2020 Dongguk Likelion</h1>
<a href="{% url 'firstapp:page_one' %}">one</a>
<a href="{% url 'firstapp:page_two' %}">two</a>
<a href="{% url 'firstapp:page_three' %}">three</a>
```
위와 같이 `url`에 `firstapp:`부분을 추가해 줍니다. 이제 `firstapp`의 `page_one` 이름을 가진 `url`로 가라는 의미가 되었습니다.  

# 왜 app_name을 설정해줘야 하나요:question:
`firstapp`의 앱 이름을 설정하지 않은 상태에서  
```bash
$ python manage.py startapp secondapp
```
명령어로 새로운 앱을 하나 더 만들어봅시다.  

## template  
그리고 `secondapp/templates/secondapp/` 디렉토리에 `one.html`이라는 파일을 만든 다음, `firstapp`의 `one.html` 파일과 구분하기 위해
```html
<h1>secondapp 1</h1>
```
라고 내용을 적어줄게요.  

## view  
`views.py`에는 `secondapp`의 `one.html`을 띄워주는 함수를 작성해줍시다.  
```python
from django.shortcuts import render

# Create your views here.
def one(request):
    return render(request, 'secondapp/one.html')
```
## url  
우리는 프로젝트의 `urls.py`에서 앱의 `urls.py`를 include하고 있기 때문에 `secondapp`의 `urls.py` 파일을 만들어주는것도 잊지 마세요.  
```python
from django.urls import path
from . import views

# app_name = 'secondapp'
urlpatterns = [
    path('one/', views.one, name="page_one"),
]
```
`page_one`이라는 같은 이름의 `path`를 만들어줍니다.  

마지막으로 프로젝트의 `urls.py`에서 `secondapp`의 `urls`를 include해줍니다.  
```python
from django.contrib import admin
from django.urls import path, include


urlpatterns = [
    path('admin/', admin.site.urls),
    path('first/', include('firstapp.urls')),
    path('second/', include('secondapp.urls')),
]

```

서버를 켜서 `localhost:8000/first/`로 들어간 다음, `one` 링크를 클릭해보세요.  
`secondapp 1`이라는 페이지가 나타나게 됩니다.  
`firstapp`의 `page_one`이라는 이름의 `url`로 연결하고 싶었는데 `secondapp`의 `page_one`이라는 이름의 `url`로 연결되었습니다.  

이처럼 개발하는 과정에서 같은 이름의 `url`이 생길 수 있기 때문에 구분해주기 위해 앱 이름을 설정하는 것이 좋습니다.  

다시 `firstapp`의 `urls.py`에 앱 이름을 설정하고, `main.html`의 코드도 수정하면 이제는 `secondapp`의 `page_one`이 아니라 `firstapp`의 `page_one`에 해당하는 `url`로 잘 이동하는 것을 확인할 수 있습니다.  
