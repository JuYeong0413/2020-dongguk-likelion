# Django-allauth 기본 제공 템플릿 변경하기  
`Django-allauth`를 통해 만들어진 페이지는 디자인이 썩 예쁜 것은 아닙니다.  
그래서 로그인, 로그아웃, 회원가입 등 직접 템플릿을 수정하고 싶은 분들께서 보시면 되겠습니다.  

## Django-allauth 템플릿  
우리가 `Django-allauth` 패키지를 설치하면 사용하게 되는 템플릿은 모두 [**여기**](https://github.com/pennersr/django-allauth/tree/master/allauth/templates/account) 있습니다.  

예시로 회원가입 페이지를 커스텀해보겠습니다.  

### myblog/templates/account/signup.html  
`Django-allauth` 템플릿을 사용하기 위해서는 파일 경로 설정이 중요합니다.  
반드시 `templates/account/` 폴더 안에 `Django-allauth`와 관련된 html 파일이 있어야 합니다.  

[회원가입 페이지 템플릿](https://github.com/pennersr/django-allauth/blob/master/allauth/templates/account/signup.html)의 내용을 복사한 다음 넣어주도록 하겠습니다.  
```html
{% extends "account/base.html" %}

{% load i18n %}

{% block head_title %}{% trans "Signup" %}{% endblock %}

{% block content %}
<h1>{% trans "Sign Up" %}</h1>

<p>{% blocktrans %}Already have an account? Then please <a href="{{ login_url }}">sign in</a>.{% endblocktrans %}</p>

<form class="signup" id="signup_form" method="post" action="{% url 'account_signup' %}">
  {% csrf_token %}
  {{ form.as_p }}
  {% if redirect_field_value %}
  <input type="hidden" name="{{ redirect_field_name }}" value="{{ redirect_field_value }}" />
  {% endif %}
  <button type="submit">{% trans "Sign Up" %} &raquo;</button>
</form>

{% endblock %}
```
기본적으로 템플릿은 모두 영어로 작성되어 있으며, `i18n`을 통해 `settings.py`에 설정된 언어로 번역되어 보여집니다. 우리가 `settings.py`에서 `LANGUAGE_CODE = 'ko-kr'`로 작성했던 것이 기억나시나요? 그래서 코드는 영어로 작성되어 있지만, 서버를 실행시켜 보면 한국어로 보이는 것을 확인하실 수 있습니다.  

회원가입 페이지에서 상단에  
```
이미 계정이 있으신가요? 바로 로그인 하세요.
```
라는 문구를 없애고 싶다고 가정해 봅시다.

위 코드에서 해당 문구는  
```html
<p>{% blocktrans %}Already have an account? Then please <a href="{{ login_url }}">sign in</a>.{% endblocktrans %}</p>
```
이 부분에 해당합니다. 지워봅시다.  
파일을 저장한 다음 회원가입 페이지를 새로고침하면 문구가 사라진 것을 확인할 수 있습니다.  
