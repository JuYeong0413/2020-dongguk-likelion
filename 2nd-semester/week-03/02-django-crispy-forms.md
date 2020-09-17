# django-crispy-forms  
[`django-crispy-forms`](https://django-crispy-forms.readthedocs.io/en/latest/)는 불필요한 중복이 없고 많은 양의 코드 작성을 지양하는 DRY(Don't Repeat Yourself) 방식의 `form`을 `Django`에서 만들 수 있게 하는 앱입니다.  

## 설치방법  
```bash
$ pip install django-crispy-forms
```

## 기본 설정  
### settings.py  
```python
INSTALLED_APPS = [
    ...
    'crispy_forms',
    ...
]

...

CRISPY_TEMPLATE_PACK = 'bootstrap4'
```
`settings.py`파일의 `INSTALLED_APPS` 부분에 `crispy_forms`를 추가해주세요.  
그리고 `CRISPY_TEMPLATE_PACK`이라는 `Django` 설정 변수에 프로젝트에 적용할 기본 템플릿 팩을 설정할 수 있습니다.  
* bootstrap  
* bootstrap3  
* bootstrap4  
* uni-form  

과 같은 템플릿 팩이 제공되고 있는데, `bootstrap4`를 추천드립니다.  

## Template  
```html
{% load crispy_forms_tags %}
...
<form method="POST">
    {{ form | crispy }}
</form>
```
`django-crispy-forms`를 이용하고자 하는 `html` 파일에 `{% load staticfiles %}` 다음 줄에 `{% load crispy_forms_tags %}` 를 추가해주세요.  
그 다음 `form` 또는 `formset` 컨텍스트 변수 뒤에 `| crispy` 필터를 추가해주면 됩니다.  
