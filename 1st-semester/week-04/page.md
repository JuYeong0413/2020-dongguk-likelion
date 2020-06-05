# HTML 페이지 띄우기  
앱을 생성하고 `settings.py`의 `INSTALLED_APPS`에 추가도 했으니, `HTML` 파일을 띄워봅시다.  

## Template  
`Django`의 `MTV` 패턴에서 `T`에 해당하는 부분입니다.  
`Template`는 사용자가 보게 될 화면의 모습을 정의합니다. 서버와 통신을 하고 그것에 대한 반응으로 `html` 등을 보여주는 것이 바로 `template`인 것이죠.  
```
firstproject/
├───manage.py
├───firstproject/
├───firstapp/
        migrations/
        templates/
                firstapp/
                        main.html
        __init__.py
        admin.py
        apps.py
        models.py
        tests.py
        views.py
```
위와 같이 앱 디렉토리인 `firstapp/` 안에 `templates` 폴더를 만들어줍시다. 그리고 `templates` 폴더 안에 앱 이름과 동일한 `firstapp`이라는 폴더를 만들어줍니다. 우리가 앞으로 앱에서 작성할 `html` 파일은 모두 `앱/templates/앱이름/` 디렉토리에 작성할 거에요.  

`main.html`이라는 파일을 만들어 줍시다.  
`Django` 프로젝트에서 화면을 잘 띄워주는지 확인하기 위해 아래와 같은 코드를 입력해줄게요.  
```html
<h1>2020 Dongguk Likelion</h1>
```

## View  
`MTV` 패턴에서 `View`는 애플리케이션의 제어 프름 및 처리 로직을 정의하는 부분입니다. 쉽게 말해서 서버에 대한 걸 처리를 하는 부분이에요.  

우리는 앞서 작성한 `main.html`을 화면에 띄워주는 처리를 해야 하겠죠?  
```python
from django.shortcuts import render

# Create your views here.
def main(request):
    return render(request, 'firstapp/main.html')
```
`Python`에서 함수를 정의할 때는 `def` 문을 사용합니다. `def`는 '정의하다'라는 뜻을 가진 `define`의 앞 글자를 딴 것이에요. `def` 문은 아래와 같은 형식으로 작성합니다.  
```python
def 함수이름(인자):
    함수를 호출했을 때 실행될 코드
```
:bulb: `Python`에서 indent(들여쓰기)는 4칸입니다. 4칸 들여쓰기를 지키지 않으면 에러가 발생합니다.  

다시 본론으로 돌아와서, `views.py` 파일의 첫 번째 줄에 있는 `from django.shortcuts import render` 부분은 `django.shortcuts` 패키지에 있는 `render` 함수를 사용한다는 의미로, `html`로 데이터를 보내기 위한 것입니다.  

`def main(request):`은 `main`이라는 이름의 함수를 만든다는 의미입니다. `request`를 인자로 받습니다.  

`return render(request, 'firstapp/main.html')`은 `render` 함수를 통해 `request`를 `firstapp/main.html` 문서로 리턴한다는 의미입니다.  

## URL  
화면에 띄워줄 `html` 파일도 작성했고, `views.py`에서 `html` 파일을 띄워주는 코드를 작성했고, 이제 마지막으로 남은 것은 어떨 때 이 화면을 띄워줄 것인가에 대한 부분이에요.  

사용자가 `url`로 접속하는 것이 바로 `request` 인데요, 웹 애플리케이션의 다양한 기능은 각각 고유한 `url`을 가지고 있고 해당 `url`에 접속함으로써 그 기능이 활성화됩니다. 사용자가 특정한 `url`에 접속해서 `request`를 보내게 되면 `view`에서 `request`를 받아 `response`를 돌려주는 것이죠. 즉 어떤 `url`에 사용자가 접속할 때 어떤 `view`로 연결하는지를 처리해보도록 하겠습니다.  

프로젝트 폴더의 `urls.py` 파일에 들어가주세요.  
```python
"""firstproject URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/3.0/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
```
주석을 읽어보면 알수있듯 `urlpatterns` 리스트는 `URL`을 `view`로 라우팅합니다.  
```
path(경로, 해당하는 view),
```
위와 같은 형식으로 작성합니다.  

3가지 방법에 대한 예시가 제공되어 있는데,  
- [함수형(function) view](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/1st-semester/week-04#%ED%95%A8%EC%88%98%ED%98%95-view)  
- 클래스형(class-based) view  
- [다른 `URLconf`를 포함하기](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/1st-semester/week-04#%EB%8B%A4%EB%A5%B8-urlconf-%ED%8F%AC%ED%95%A8%ED%95%98%EA%B8%B0)  

가 있습니다.  

클래스형 view는 다음에 시간이 되면 알려드리고, 우선 간단한 함수형 view부터 알아봅시다.  

### 함수형 view  
```
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
```
우선 앱의 `views`를 import 해 온 다음 `urlpatterns`에 `URL`을 추가하면 됩니다.  
```python
from django.contrib import admin
from django.urls import path
from firstapp import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.main),
]
```
`from firstapp import views`로 `firstapp` 앱의 `views`를 import 해오는 코드를 추가했습니다.  

그리고 `path`를 추가해줬는데, 제일 앞의 `''`는 `url`을 의미합니다. `''`는 비어있는 문자열이므로, 사용자가 아무것도 입력하지 않은 경로에 접속했을 때 `views.main`으로 가서 요청을 처리하겠다는 것입니다. 여기서 `views.main`은 위쪽에서 import한 `firstapp`의 `views`의 `main` 함수를 의미합니다. 경로에 `/`을 붙이는 것과 마지막에 쉼표를 추가하는 것도 잊지 마세요!  

이제 페이지를 띄우기 위한 모든 작업이 끝났습니다.
```bash
$ python manage.py runserver
```
명령어로 서버를 실행시킨 다음 `localhost:8000` 또는 `127.0.0.1:8000`으로 접속해보면 `2020 Dongguk Likelion` 이라는 글자가 출력되는 화면이 표시될 겁니다.  

### 다른 `URLconf` 포함하기  
위와 같은 방법으로 프로젝트의 `urls.py`에 모든 `path`를 적어준다면, 페이지가 늘어날수록 `path`의 가독성도 떨어지고 유지보수도 어렵겠죠? 그래서 이 방법을 이용하실 것을 권장합니다. 바로 `url`을 앱에서 import해서 사용하는 방법입니다. 쉽게 말해서, `url`을 앱에서 적절하게 나눠서 관리하는 방법입니다.  

우리가 `python manage.py startapp firstapp`으로 새로운 앱을 만들었을 때, `firstapp/` 디렉토리에는 `urls.py` 파일이 생성되지 않습니다. 이제 우리가 직접 앱의 디렉토리에 `urls.py` 파일을 만들어서 `urlpatterns`를 작성해 준 다음, 그 파일을 프로젝트의 `urls.py`에서 불러와서 사용할 거에요.  

`firstapp/` 디렉토리 안에 `urls.py` 파일을 만든 다음, 아래 코드를 입력해 주세요.  
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.main),
]
```
첫 줄의 `from django.urls import path`는 프로젝트의 `urls.py`에도 포함되어 있는 부분입니다. 우리가 따로 앱의 `url`을 작성해주고 있고, 여기서 `path`를 정의할 것이니 똑같이 import 해 옵시다.  

그리고 앞서 함수형 view에서 `from firstapp import views`라고 작성했던 부분이 `from . import views`로 바뀌었어요. `.`은 현재 디렉토리를 의미합니다. 프로젝트의 `urls.py`에서는 `firstapp`이라는 앱의 `views`를 import한다고 알려줘야 했지만, 지금 우리가 작업하는 `urls.py`는 `firstapp/` 안에 있는 파일이기 때문에(`firstapp`의 `views.py`와 같은 디렉토리에 위치한 파일이기 때문에) `.`를 입력하는 것입니다.  

그 다음에는 `urlpatterns`를 입력해주면 됩니다. 프로젝트의 `urls.py`에 있는 `urlpatterns`처럼 리스트(`[]`) 형태로 입력을 해 주면 되는데요, 우리는 아무것도 입력하지 않은 주소에 접속했을 때 화면을 띄워줄 것이기 때문에 `path('', views.main),`이라고 작성해 줄게요. 함수형 view 부분에서 입력한 코드와 동일합니다.  
이제 프로젝트의 `urls.py`에서 우리가 새로 작성한 앱의 `urls.py`를 `include`해 주어야 합니다. 다시 프로젝트의 `urls.py` 파일에 가서 아래와 같이 코드를 수정해줍시다.  
```python
from django.contrib import admin
from django.urls import path, include


urlpatterns = [
    path('admin/', admin.site.urls),
    path('first/', include('firstapp.urls')),
]
```
`firstapp`의 `views`를 import 했던 걸 지우고, 앞으로 `firstapp`의 `url`을 include하는 방식을 사용할 것이기 때문에 `from django.urls import path, include`로 `include`를 사용할 수 있게 추가해줍시다.  

`urlpatterns` 부분에는 `firstapp`의 `urls.py`를 include하는 코드를 작성해주면 됩니다. `path('first/', include('firstapp.urls')),` 코드는 `first/` 경로 뒤로 `firstapp` 앱의 `urls.py`를 include한다는 의미입니다. 즉, `firstapp`에 관한 `urlpattern`은 전부 `first/`로 시작을 한다는 뜻입니다.  

이제 페이지를 띄우기 위한 모든 작업이 끝났습니다.
```bash
$ python manage.py runserver
```
명령어로 서버를 실행시킨 다음 `localhost:8000` 또는 `127.0.0.1:8000`으로 접속해보면, `Page not found (404)` 라는 에러 페이지가 보일거에요. 바로 `url`을 include하는 과정에서 `path('first/', include('firstapp.urls')),`라고 코드를 작성했기 때문에 우리는 `localhost:8000` 또는 `127.0.0.1:8000`가 아니라, `localhost:8000/first/` 또는 `127.0.0.1:8000/first/`라는 주소로 접속해야 합니다.  

이제 `2020 Dongguk Likelion` 글자가 잘 나오는 것을 확인할 수 있습니다.  

### 정리  
사용자가 특정 `url`로 접속을 하면 그 경로에 해당하는 `url`을 `Django`에서 찾습니다.  `localhost:8000/first/` 또는 `127.0.0.1:8000/first/`를 입력하면 `first/`로 시작하는 `url`인 `path('first/', include('firstapp.urls')),`를 찾게 되는 것이죠. 이 코드는 `firstapp`의 `urls.py`로 가라고 알려주고 있습니다. `firstapp/urls.py`로 이동하고 나서 사용자가 접속한 `url`이 `firstapp/` 뒤에 아무것도 입력하지 않은 것이기 때문에 여기서 `path('', views.main),`를 찾고, `views`의 `main` 함수로 가라고 알려주고 있으니 `views.py`의 `main`함수를 찾습니다.  

앞서 `views.py`의 `main`함수에서는 `main.html`을 띄워주는 코드를 작성했었죠? 그래서 `return render(request, 'firstapp/main.html')`부분을 통해 `template` 즉 `firstapp/main.html` 파일을 사용자에게 보여주게 됩니다.  

URL에 접속해서 보내는 요청 -> `urls.py` -> 해당 앱의 `urls.py` -> `views` -> `templates`(`html` 파일)을 응답으로 보냄  

이런 과정이라고 보시면 되겠습니다.  
