# 좋아요 누른 글 목록 확인하기  
좋아요 기능이 생겼으니, 내가 어떤 글에 좋아요를 눌렀는지 모아서 확인할 수 있는 페이지가 있으면 좋겠죠?  
아주 간단합니다. 우선 좋아요 누른 글 목록 페이지는 기존의 게시글 목록 페이지와 형태를 동일하게 만들어 줄 것이기 때문에 `posts/templates/posts/main.html` 파일의 내용을 복사해주세요.  

## Template
### posts/templates/posts/like_list.html  
좋아요 누른 글 목록을 띄워줄 새로운 html 파일을 만들었습니다.  
복사한 코드를 붙여넣겠습니다.  

그런데 이 코드를 그대로 사용하면 안 됩니다. 우리는 **좋아요를 누른** 글 목록을 원하기 때문에, 아래와 같이 템플릿 태그 부분을 모두 수정해 주겠습니다.  
```html
{% extends 'base.html' %}
{% load static %}

{% block content %}
{% include 'shared/_navbar.html' %}
<div class="container">
    {% if user.is_authenticated %}
        <h1>좋아요를 누른 글 목록</h1>
        <div class="row">
            {% for like in likes %}
                <div class="col-md-4 col-12">
                    <div class="card" style="width: 18rem; margin-top: 20px;">
                        <div class="card-body">
                            <h5 class="card-title">{{ like.post.title }}</h5>
                            <p>조회수 : {{ like.post.view_count }}</p>
                            <a href="{% url 'posts:show' like.post.id %}" class="btn btn-primary">상세보기</a>
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
`View`에서 `likes`라는 이름의 변수로 좋아요한 글 전체를 가져올 겁니다. 그리고 `like`라는 변수에 하나씩 담아 `for`문을 이용해 띄워줄거에요.  
`Like` 모델에 연결된 `post`(게시글)를 가져오는 방식이기 때문에 템플릿 태그에서 `like.post.title` 처럼 `like`를 앞에 붙여서 게시글에 대한 정보를 불러와야 한다는 점에 유의해주세요.  

## View  
### posts/views.py  
#### 방법 #1  
```python
@login_required
def like_list(request):
    likes = Like.objects.filter(user=request.user)
    return render(request,'posts/like_list.html',{'likes': likes})
```
좋아요를 누른 글 목록을 가져오는 첫 번째 방법은 `Like` 모델을 이용하는 방법입니다.  
좋아요 객체 중에서 `user`에 요청을 보낸 사용자가 해당하는 객체들만 `fliter`를 해서 `likes`라는 변수에 담아주고, 앞서 만든 `like_list.html` 파일로 `likes`라는 이름으로 해당 변수를 같이 보내줍니다.  
:bulb: `{'likes': likes}`에서 왼쪽의 `likes`는 우리가 html에서 템플릿 태그를 이용해 사용할 이름이고, 오른쪽의 `likes`는 `Like` 모델에 `fliter`를 통해 좋아요를 누른 글 목록을 담아둔 `likes` 변수입니다.  

#### 방법 #2  
```python
@login_required
def like_list(request):
    likes = request.user.like_set.all()
    return render(request,'posts/like_list.html',{'likes': likes})
```
두 번째 방법은 사용자에 연결된 `Like`를 가져오는 방법입니다.  
`Like` 모델에서 `User` 모델이 `ForeignKey`로 정의되어 있다는 점을 이용하는 방법인데요, 요청을 보낸 사용자(`request.user`)와 연결된 `Like`를 `like_set.all()`을 이용해서 좋아요 누른 글을 모두 가져와 `likes`라는 변수에 담습니다. 마찬가지로, `like_list.html`파일로 `likes`라는 이름으로 해당 변수를 같이 보내줍니다.  
:bulb: `like_set`이 이해가 되지 않는다면 [1:N 모델 관계 개념 복습자료](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/summer-semester/week-04/01-model-relations.md#1n-%EA%B4%80%EA%B3%84---foreignkey)를 다시 읽어보세요!  

## URL  
### posts/urls.py  
```python
from django.urls import path
from .views import *

app_name="posts"
urlpatterns = [
    ...
    path('like_list/', like_list, name="like_list"),
]
```
`path`까지 추가해주면 끝!

## Template  
### myblog/templates/shared/_navbar.html  
```html
<li class="nav-item">
    <a class="nav-link" href="{% url 'posts:like_list' %}">Like</a>
</li>
```
`navbar`에 좋아요를 누른 글 목록 페이지로 이동할 수 있게 링크를 추가해 주었습니다.  
좋아요를 누른 글 목록이 잘 나오는 것을 확인할 수 있습니다.  
