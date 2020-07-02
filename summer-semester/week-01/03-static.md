# static  
`static`은 정적 파일을 의미합니다. 정적 파일이란 프로젝트 안에 있으면서 계속 재사용할 수 있는 CSS, JavaScript, image, font 등을 의미하는데요, 사용자가 업로드하는 것이 아니라 우리 프로젝트에서 미리 준비되어 있어서 사용자에게 띄워주는 정적 파일들을 관리하는 방법에 대해서 알아보도록 하겠습니다.  

### settings.py  
`static` 역시 `settings.py`에 경로 설정을 해야 합니다.  
```python
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/3.0/howto/static-files/

STATIC_URL = '/static/'
```
`settings.py` 맨 아래에 보시면 `static`에 대한 부분이 있을 거에요.  
여기 `STATIC_URL`이 이미 작성되어 있는데요, 아래쪽에 코드를 추가해 줄 겁니다.  
```python
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'myblog', 'static')
]
```
어디서 많이 본 것 같은 코드죠? 우리가 `extends`를 사용하기 위해 `templates`의 `DIRS`를 설정해 준 부분과 비슷합니다.  
우리 프로젝트 폴더인 `myblog/` 폴더 안에 `static/` 폴더에서 static files를 관리하겠다는 의미입니다.  

`static/` 폴더에서 모든 정적 파일을 관리해도 문제가 생기는 건 아니지만 한 폴더에 CSS, JavaScript, image 등 다양한 파일을 구분없이 두면 지저분해 보이니까 저는 `static/` 안에 따로 `css/`와 `images/` 폴더를 만들어 줄게요.  
```
myblog/
├───manage.py
├───myblog/
        templates/
        static/
                css/
                images/
        __init__.py
        asgi.py
        settings.py
        urls.py
        wsgi.py
```
이런 구조로 프로젝트 차원에서 정적 파일을 관리할 겁니다.  
(앱 단위로 관리를 하려면 `settings.py`에서 설정한 `STATICFILES_DIRS`에서 `myblog` 대신 앱 이름을 적어주시고, `static/` 폴더를 앱 폴더 안에 생성해 주면 됩니다.)
이제 `css/` 폴더에 우리 프로젝트 전체에 적용시킬 CSS 파일을 하나 만들어볼게요.  

### project.css  
```css
h1 {
    color: orange;
}
```
`<h1>` 태그로 감싼 텍스트를 주황색으로 바꿔볼게요.  
모든 템플릿에 적용시키기 위해 `base.html`에 CSS파일을 추가해 줍시다.  

### base.html
```
<link rel="stylesheet" href="{% static 'css/project.css' %}
```
`<head>`의 맨 아래쪽에 추가하면 CSS를 불러오게 될 거에요.  
그리고 `Django` 프로젝트에서 `static`을 사용하려면 템플릿 태그를 상단에 달아줘야 합니다.  
```python
{% load static %}
```
새로고침을 하면 `<h1>` 태그의 글자 색깔이 바뀐 것을 확인할 수 있습니다.  

이미지도 같은 방법으로 추가를 해 주시면 됩니다.  
`static/images/`에 `profile.png`라는 사진을 넣었다고 하면,  
```html
<img src="{% static 'images/profile.png' %}" alt="profile image">
```
와 같이 원하는 위치에 `<img>` 태그로 이미지를 삽입한 다음, 해당 이미지가 있는 html 파일 상단에  
```python
{% load static %}
```
을 추가해 주시면 됩니다.  

:bulb: `extends`로 html을 상속받으면서 `static`을 사용하는 경우  
```python
{% extends 'base.html' %}
{% load static %}

{% block content %}
...
{% endblock %}
```
의 순서로 작성을 해 주시면 됩니다.  
