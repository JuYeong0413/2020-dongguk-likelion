# 조회수 기능 구현  
우리가 만든 `Post` 모델에 `view_count`라는 이름으로 조회수 속성을 만든 것 기억하시나요?  
모델링을 해 두었는데 지금까지 전혀 사용하지 않던 속성이었죠.  
이번에 조회수가 증가되는 기능을 구현해봅시다.  

## Template  
### posts/templates/main.html  
게시글 제목을 표시해주는 곳 아래에 조회수도 표시되게 하겠습니다.
```html
<p>조회수 : {{ post.view_count }}</p>
```
만 넣으면 끝입니다.  

우리가 게시글 목록을 볼 수 있는 메인 페이지에서 `posts`로 받아온 전체 게시글을 `post`라는 이름으로 `Python` `for`문을 이용해서 하나씩 카드로 표시되게 하고 있었죠. 제목이 표시되는 것과 마찬가지로 `.`을 이용해서 조회수 속성이 표시되게 할 수 있습니다.  

전체 코드는 아래와 같습니다.  
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
                            <p>조회수 : {{ post.view_count }}</p>
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

### posts/templates/posts/show.html  
상세보기 페이지에서도 조회수를 보여줍시다.  
저는 작성자 아래쪽에 코드를 추가할게요.  
```html
<h3>조회수 : {{ post.view_count }}</h3>
```
전체 코드는 아래와 같습니다.  
```html
{% extends 'base.html' %}

{% block content %}
{% include 'shared/_navbar.html' %}
<div class="container">
    <h1>글 #{{ post.id }} 상세보기</h1>
    <h3>글 제목 : {{ post.title }}</h3>
    {% if post.writer %}
        <h3>작성자 : {{ post.writer }}</h3>
    {% else %}
        <h3>작성자 : 알 수 없음</h3>
    {% endif %}
    <h3>조회수 : {{ post.view_count }}</h3>
    <h3>글 내용 : {{ post.content }}</h3>
    <h3>작성 시각 : {{ post.created_at }}</h3>
    <h3>수정 시각 : {{ post.updated_at }}</h3>
    {% if post.image %}
        <img src="{{ post.image.url }}" alt="사진">
    {% endif %}

    {% if user == post.writer %}
        <a href="{% url 'posts:update' post.pk %}" class="btn btn-primary">수정</a>
        <a href="{% url 'posts:delete' post.pk %}" class="btn btn-danger">삭제</a>
    {% endif %}
</div>
{% endblock %}
```

지금은 조회수가 증가하는 기능이 추가되지 않았기 때문에 우리가 모델링을 할 때 설정해 둔 초기값인 `0`에서 변하지 않습니다.  
게시글 상세보기를 할 때 조회수가 1씩 증가되어야 하니 `views.py`의 `show` 함수에서 한번 구현해봅시다.  

## View  
### posts/views.py  
```python
def show(request, post_id):
    post = Post.objects.get(pk=post_id)
    return render(request, 'posts/show.html', {'post': post})
```
지금은 `post_id`를 이용해 `Post` 객체를 가져와서 `post`라는 변수에 담은 다음 html에 보내고 있네요.  
우리는 `post`에 담긴 게시글의 조회수, 다시 말해 `view_count`를 `1`만큼 증가시키면 됩니다.  
```python
def show(request, post_id):
    post = Post.objects.get(pk=post_id)
    increased_view_count = post.view_count + 1
    post.view_count = increased_view_count
    post.save()
    return render(request, 'posts/show.html', {'post': post})
```
`post`에 우리가 상세보기 할 게시글 객체가 담겨있으니 `.`을 이용해 `view_count` 속성에 접근한 후 `1`만큼 값을 증가시켜 줍니다.  
증가된 값을 저는 `increased_view_count`라는 변수에 담아주었어요.  
기존의 `view_count`값을 `increased_view_count`로 대체해야겠죠? `post.view_count = increased_view_count` 부분이 바로 그 역할을 합니다.  

:bulb: 프로그래밍에서 `=`은 *같다*는 의미가 아닌 **대입**의 의미입니다.  
(같다는 것은 `==`를 사용합니다. `if request.method == "POST":` 처럼요.)  

그리고 우리가 `update` 기능을 구현할 때 했던 것처럼, 객체의 속성값이 변경되었기 때문에 `save()`를 이용해 저장을 해야 합니다.  

그런데 이 코드를 조금 더 간단하게 작성하는 방법을 알려드릴게요.  
위에서는 `increased_view_count`라는 새로운 변수를 만들었지만, 그냥 기존의 `view_count`값을 바로 대체해 버리는 것이죠.  
```python
def show(request, post_id):
    post = Post.objects.get(pk=post_id)
    post.view_count = post.view_count + 1
    post.save()
    return render(request, 'posts/show.html', {'post': post})
```
`=`을 기준으로 오른쪽의 `post.view_count`는 기존의 `post`에 담긴 게시글의 조회수입니다. 기존 조회수에 `1`을 먼저 더해준 다음 그 값을 대입 연산자(`=`)를 통해 `post`의 `view_count`에 넣어주는 겁니다.  
즉, `=` 왼쪽의 `post.view_count`는 `1`이 증가된 조회수가 되는 것이죠.  

그런데 놀랍게도 이 상태에서 코드를 압축시킬 수 있습니다.  
```python
def show(request, post_id):
    post = Post.objects.get(pk=post_id)
    post.view_count += 1
    post.save()
    return render(request, 'posts/show.html', {'post': post})
```
바로 이렇게요!  
`+=` 연산자가 생소하실 수 있는데, 예시를 하나 들어보자면  
```python
a += b
```
는  
```python
a = a + b
```
와 같은 의미입니다.  
우리가 앞에서  
```python
post.view_count = post.view_count + 1
```
로 코드를 간단하게 작성했는데요, 바로 위에 `a += b`가 `a = a + b`와 같다는 예시와 같은 형태죠.  
그래서 `post.view_count += 1`의 형태로 한번 더 우리가 압축시킬 수 있답니다.  
