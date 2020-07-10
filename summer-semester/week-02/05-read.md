# Read 기능 구현  
글 작성(create)과 모든 글 목록을 볼 수 있는 페이지를 만들었으니, 이제 각각의 글을 자세하게 볼 수 있는 상세보기 페이지를 만들어봅시다.  

## Template  
### posts/templates/posts/show.html  
```html
{% extends 'base.html' %}

{% block content %}
{% include 'shared/_navbar.html' %}
<div class="container">
    <h1>글 #{{ post.id }} 상세보기</h1>
    <h3>글 제목 : {{ post.title }}</h3>
    <h3>글 내용 : {{ post.content }}</h3>
    <h3>작성 시간 : {{ post.created_at }}</h3>
</div>
{% endblock %}
```
`posts`의 `main` 페이지에서 `Post` 객체를 `posts`라는 이름으로 받아온 것처럼, 상세보기 페이지에서는 특정한 객체를 `post`라는 이름으로 받아올거에요.  
마찬가지로 `{{ }}` 태그를 이용해 객체의 속성 값을 표시해주도록 하겠습니다.  

## View  
### posts/views.py  
```python
def show(request, id):
    post = Post.objects.get(pk=id)
    return render(request, 'posts/show.html', {'post': post})
```
`show`라는 함수를 추가해 주었습니다.  

우리가 특정한 글을 가져오려면, 해당 글의 고유한 `id`값을 알아야 하겠죠? 그래서 지금까지 작성했던 다른 함수들과는 다르게 `id`를 받아와야 합니다.  
그 다음, 받아온 `id`값을 이용해 `Post` 객체들 중에서 해당하는 `id`값의 `Post` 객체 하나를 꺼내와 `post`라는 이름의 변수에 담아줍니다.  
:bulb: `pk`는 기본키(primary key)로, `id`와 같은 의미입니다.  

`post` 변수에 담긴 `Post` 객체를 `posts/show.html`로 보낼 때, 우리가 앞서 `Template`에서 `post`라는 이름으로 받아올 것이라고 했기 때문에, `post`라는 키(key)값을 가진 딕셔너리(dictionary) 형태로 보내줍니다.  

## URL  
### posts/urls.py  
```python
from django.urls import path
from .views import new, create, main, show

app_name="posts"
urlpatterns = [
    path('new/', new, name="new"),
    path('create/', create, name="create"),
    path('', main, name="main"),
    path('<int:id>/', show, name="show"),
]
```
`urls.py`에도 `show`를 추가해 줘야겠죠?  

여기서 주목해야 하는 건 `show`에 `<int:id>/`가 붙는다는 점입니다.  
`id`라는 이름을 가진 `integer`(정수) 타입의 값을 주소로 보낸다는 의미입니다.  

우리가 `views.py`에서 `show` 함수를 호출할 때 `id`값을 받아온다고 말씀을 드렸는데요, 여기서 `id`가 바로  
```python
def show(request, id):
```
이 부분의 `id`입니다.  

만약에 `path`를  
```python
path('<int:post_id>/', show, name="show"),
```
라고 작성해 준다면  
`views.py`에서도  
```python
def show(request, post_id):
    post = Post.objects.get(pk=post_id)
    return render(request, 'posts/show.html', {'post': post})
```
와 같이 작성해주셔야 합니다.  

이제 상세보기 기능 구현이 완료되었으니, `localhost:8000/posts/1`이라는 주소로 들어가 봅시다.  
`id`가 `1`인 `Post`객체의 상세보기 페이지가 잘 나오는 걸 확인할 수 있습니다.  

## Template  
### posts/templates/main.html  
게시글 목록에서 상세보기 페이지로 이동할 수 있는 버튼의 링크를 연결해 줍시다.  
```html
<a href="#" class="btn btn-primary">상세보기</a>
```
비워두었던 `a` 태그의 `href` 속성에 URL을 연결시켜 줄 거에요.  
```html
<a href="{% url 'posts:show' post.pk %}" class="btn btn-primary">상세보기</a>
```
`posts`의 `show`로 가야 하는데, 앞서 언급했던 것처럼 우리는 어떤 글을 보여줄 것인지 알아야 하기 때문에 `post`의 `pk`, 즉 `id`값을 URL 뒤에 붙여서 보내줘야 합니다.  

`pk`가 `id`와 같은 의미이기 때문에 위 코드는  
```html
<a href="{% url 'posts:show' post.id %}" class="btn btn-primary">상세보기</a>
```
로 작성해도 동일합니다.  

이제 글 목록 페이지에서 버튼을 통해 특정 글의 내용을 상세하게 확인할 수 있게 되었습니다. :clap:  
