# [Django-allauth](https://github.com/pennersr/django-allauth)  
`Django-allauth` 패키지는 사용자 관리를 편하게 할 수 있도록 도와줍니다.  
회원가입, 로그인, 로그아웃, 비밀번호 변경, 소셜로그인 등의 기능을 제공합니다.  
url, view, html template이 내장되어 있어 우리가 따로 생성할 필요가 없습니다.  

## 설치방법  
```bash
$ pip install django-allauth
```

## `Django-allauth`에 대한 설정 추가  
### myblog/settings.py  
```python
INSTALLED_APPS = [
    ...
    'django.contrib.sites',
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
]
```
이렇게 네 줄을 추가해줍니다.  
`django.contrib.sites`는 migrate를 진행할 때 필요합니다.  
`allauth`, `allauth.account`, `allauth.socialaccount`는 모두 `Django-allauth` 관련된 앱입니다.  
`allauth.account`는 가입한 계정을 관리할 때, `allauth.socialaccount`는 소셜 계정으로 가입한 계정을 관리할 때 사용됩니다.  

`settings.py` 파일 맨 아래로 내려와서 아래 코드를 추가해줍시다.  
```python
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'allauth.account.auth_backends.AuthenticationBackend',
]
SITE_ID=1
LOGIN_REDIRECT_URL = '/'
```
로그인 후 리다이렉트해주는 기본 url은 `/accounts/profile`로 지정되어 있는데 이를 메인 페이지인 `/`로 지정해 주었습니다. 다른 주소를 원한다면 해당 url의 이름을 적어주시면 됩니다.  

### myblog/urls.py  
```python
urlpatterns = [
    ...
    path('accounts/', include('allauth.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```
`Django-allauth`에 관련된 라우팅을 추가해줍니다.  

### migrate  
```bash
$ python manage.py migrate
```

### myblog/templates/shared/_navbar.html  
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <a class="navbar-brand" href="{% url 'main' %}">My Blog</a>
  
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav mr-auto">
        <li class="nav-item active">
          <a class="nav-link" href="{% url 'main' %}">Home <span class="sr-only">(current)</span></a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{% url 'introduction:profile' %}">Profile</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{% url 'posts:main' %}">Post</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{% url 'posts:new' %}">New</a>
        </li>
        {% if user.is_authenticated %}
        <li class="nav-item">
          <a class="nav-link" href="{% url 'account_logout' %}">로그아웃</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{% url 'account_change_password' %}">비밀번호 변경</a>
        </li>
        {% else %}
        <li class="nav-item">
          <a class="nav-link" href="{% url 'account_login' %}">로그인</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{% url 'account_signup' %}">회원가입</a>
        </li>
        {% endif %}
    </div>
</nav>
```
전체 코드는 위와 같습니다. 우리가 새로 추가한 부분만 따로 살펴볼게요.  
```html
{% if user.is_authenticated %}
<li class="nav-item">
  <a class="nav-link" href="{% url 'account_logout' %}">로그아웃</a>
</li>
<li class="nav-item">
  <a class="nav-link" href="{% url 'account_change_password' %}">비밀번호 변경</a>
</li>
{% else %}
<li class="nav-item">
  <a class="nav-link" href="{% url 'account_login' %}">로그인</a>
</li>
<li class="nav-item">
  <a class="nav-link" href="{% url 'account_signup' %}">회원가입</a>
</li>
{% endif %}
 ```
 `Django`에서 제공하는 `User` 모델에는 인증된 사용자인지 확인하는 `is_authenticated` 속성이 있습니다. 이 속성은 모든 `User` 객체가 `True`값을 가지고 있습니다.  
 
 `user`는 현재 로그인 한 사용자의 정보를 가지고 있습니다. 즉, 유저가 로그인을 한 상태라면 `user.is_authenticated`는 `True`가 되어 navbar에 `로그아웃`과 `비밀번호 변경`이 보여지게 됩니다.  
 
 하지만 만약 사용자가 로그인이 되지 않은 상태라면 `user.is_authenticated`는 `False`가 되어 `else`부분으로 넘어가게 되고, navbar에 `로그인`과 `회원가입`이 보여지게 됩니다.  
 
 각각의 `url`은 `Django-allauth`에서 지정되어 있는 것이기 때문에 깊게 의문을 가지지 마시고 이렇게 써야 하는구나~ 정도로 생각해 두시면 되겠습니다.  
 
 ### posts/templates/posts/main.html  
 로그인 한 사용자만 게시글 목록을 확인할 수 있도록 만들어 보겠습니다.  
 ```html
 {% extends 'base.html' %}
{% load static %}

{% block content %}
{% include 'shared/_navbar.html' %}
<div class="container">
    {% if user.is_authenticated %}
        <h1>글 목록</h1>
        <div class="row">
            {% for post in posts %}
                <div class="col-md-4 col-12">
                    <div class="card" style="width: 18rem; margin-top: 20px;">
                        <div class="card-body">
                            <h5 class="card-title">{{ post.title }}</h5>
                            <a href="{% url 'posts:show' post.id %}" class="btn btn-primary">상세보기</a>
                        </div>
                    </div>
                </div>
            {% endfor %}
        </div>
    {% else %}
        <h3>로그인 후 이용이 가능합니다.</h3>
        <a href="{% url 'account_login' %}" class="btn btn-warning">로그인</a>
        <a href="{% url 'account_signup' %}" class="btn btn-warning">회원가입</a>
    {% endif %}
</div>
{% endblock %}
```
