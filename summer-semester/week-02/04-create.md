# Create 기능 구현  
앞서 글 작성하는 페이지를 만들었으니, 이제 진짜로 글을 작성하는 기능을 구현해봅시다.  

## View  
### posts/views.py  
```python
from django.shortcuts import render
from .models import Post

def new(request):
    return render(request, 'posts/new.html')


def create(request):
    if request.method == "POST":
        post_title = request.POST.get('title')
        post_content = request.POST.get('content')
        Post.objects.create(title=post_title, content=post_content)
    return render(request, 'main.html')
```
`create`라는 이름의 함수를 새로 만들어주었습니다.  
`Post` 객체를 만들어 줄 거니까 상단에 `Post` 모델을 `import`해오는 것도 잊지 말아주세요.  

우리는 앞서 `form`의 `method`를 `POST`로 지정해 주었기 때문에, 사용자가 요청한 `method`가 `POST`인 경우에만 글을 생성해 줄 거에요. 그래서 `if`문을 이용해서 처리를 해 주었습니다.  

그 다음 `POST` 방식으로 날아온 값(파라미터)을 가져와서 `Post` 객체를 만들어 줄 거에요. `request.POST.get(name)`을 이용하면 값을 가져올 수 있습니다.  

`input` 태그에 `name` 속성이 들어가는 것을 기억하고 계신가요? 여기서 `name` 부분이 바로 `input` 태그의 `name` 속성이 들어가는 자리입니다. `title`과 `content`를 가져와서 각각 `post_title`, `post_content`라는 이름의 변수에다가 넣어줄게요.  

이제 `title`값과 `content`값을 다 가져왔으니, 객체를 생성해 줄게요. 예전에 `shell`에서 `ORM`을 이용했던 걸 여기서 사용하면 됩니다.  
`Post` 객체를 새로 생성(`create`)할 건데, `title`의 값으로는 `post_title`, `content`의 값으로는 `post_content`를 넣어줄 거에요.  

마지막으로 우리의 블로그 메인 페이지로 가도록 코드를 작성해 줄게요.  

## URL  
### posts/urls.py
```python
from django.urls import path
from .views import new, create

app_name="posts"
urlpatterns = [
    path('new/', new, name="new"),
    path('create/', create, name="create"),
]
```
`views.py`에 함수를 작성해 주었으니 `urls.py`에도 추가해 줍니다.  
우리는 이번에 `views`의 함수를 개별적으로 `import`해오고 있기 때문에, 상단에 `create`를 `import`해 오는 걸 잊지 말아주세요.  

### posts/templates/posts/new.html  
```html
<form action="#" method="POST">
```
이전에 `form` 태그에서 비워두었던 `action` 부분을 채워줍시다.  
```html
<form action="{% url 'posts:create' %}" method="POST">
```

<details>
  <summary><b>:bulb: create 함수 return 부분의 indent</b></summary>  
  
  ### posts/views.py  
  `create` 함수의 코드를 보면
  ```python
  def create(request):
      if request.method == "POST":
          post_title = request.POST.get('title')
          post_content = request.POST.get('content')
          Post.objects.create(title=post_title, content=post_content)
          return render(request, 'main.html')
  ```
  가 아니라  
  ```python
  def create(request):
      if request.method == "POST":
          post_title = request.POST.get('title')
          post_content = request.POST.get('content')
          Post.objects.create(title=post_title, content=post_content)
      return render(request, 'main.html')
  ```
  로 작성을 했습니다.  

  첫 번째 방법으로 코드를 작성하게 되면, `return` 부분은 요청 방식이 `POST`일때만 실행이 됩니다.  
  따라서 우리가 `URL`을 직접 입력해 `GET` 방식으로 `localhost:8000/posts/create/`로 접근했을 때의 처리를 해 주어야 합니다.   

  두 번째 방법에서는, 사용자의 요청 방식이 `POST`가 아니라면 `return` 부분을 통해 그냥 `main.html`을 화면에 띄워주는 역할을 하게 됩니다.  
  만약 `POST` 방식의 요청이 들어온다면 `title`과 `content`를 가져와서 새로운 `Post` 객체를 만들고, `if`문을 탈출한 다음 `return`문을 통해 `main.html`로 갑니다.  

  필요에 따라 아래와 같이 코드를 작성하셔도 됩니다.  
  ```python
  def create(request):
      if request.method == "POST":
          post_title = request.POST.get('title')
          post_content = request.POST.get('content')
          Post.objects.create(title=post_title, content=post_content)
          return render(request, 'main.html')
      return render(request, 'introduction/profile.html')
  ```
  이 코드는 `POST`가 아닌 방식으로 요청이 들어오면 `introduction/profile.html`을 화면에 띄워주고, `POST` 방식으로 요청이 들어오면 `Post` 객체를 생성한 다음 `main.html`로 가는 역할을 합니다.  

  **:bulb: 참고**   
  ```python
  def create(request):
      if request.method == "POST":
          post_title = request.POST.get('title')
          post_content = request.POST.get('content')
          Post.objects.create(title=post_title, content=post_content)
          return render(request, 'main.html')
  ```
  만약 위와 같이 작성하게 되면, `URL`을 직접 입력해 `GET` 방식으로 `localhost:8000/posts/create/`로 접근했을 경우 에러가 표시됩니다.  
  `POST` 방식에 대한 `return`만이 정의되어 있기 때문입니다.  
</details>


자, 이제 새로운 글을 작성해 봅시다.  
글 작성 버튼을 누르면 메인 페이지로 이동하긴 하는데, 글 생성이 잘 되었는지 확인할 수 있게 전체 글 목록을 보여주는 페이지를 만들어 볼게요.  

## View  
### posts/views.py  
```python
from django.shortcuts import render, redirect
from .models import Post

def new(request):
    return render(request, 'posts/new.html')


def create(request):
    if request.method == "POST":
        title = request.POST.get('title')
        content = request.POST.get('content')
        Post.objects.create(title=title, content=content)
    return redirect('posts:main')


def main(request):
    all_posts = Post.objects.all()
    return render(request, 'posts/main.html', {'posts': all_posts})
```
`create`함수 아래쪽에 `main`이라는 함수를 새로 만들어 주었습니다.  

`all_posts`라는 변수에 `Post` 객체를 모두 불러와서 담은 다음, `posts`앱의 `main.html`이라는 파일에 `posts`라는 이름으로 보내줍니다.  
:bulb: `Django`에서 `render`의 세 번째 인자로는 `Python`의 딕셔너리(dictionary) 타입의 값을 넣어줘야 합니다.  

또 주목해야 할 점은 첫 번째 줄에 `redirect`도 `import`가 된 걸 볼 수 있는데요, `render`는 html을 띄워주는 역할을 하는 반면 `redirect`는 다른 액션(함수)로 가는 역할을 합니다.  
쉽게 말해, `URL`로 이동하는 겁니다.  
우리가 앞서 `create`에서 블로그 메인 페이지로 이동하도록 했기 때문에, 글 목록을 보여주는 `posts`의 `main`으로 가도록 `return`부분을 수정해 줄게요.  

## URL  
### posts/urls.py  
```python
from django.urls import path
from .views import new, create, main

app_name="posts"
urlpatterns = [
    path('new/', new, name="new"),
    path('create/', create, name="create"),
    path('', main, name="main"),
]
```
`main`에 대한 부분을 추가해줍니다.  

## Template  
### posts/templates/posts/main.html  
```html
{% extends 'base.html' %}
{% load static %}

{% block content %}
{% include 'shared/_navbar.html' %}
    <div class="container">
        <h1>글 목록</h1>
        <div class="row">
            {% for post in posts %}
                <div class="col-md-4 col-12">
                    <div class="card" style="width: 18rem; margin-top: 20px;">
                        <div class="card-body">
                            <h5 class="card-title">{{ post.title }}</h5>
                            <a href="#" class="btn btn-primary">상세보기</a>
                        </div>
                    </div>
                </div>
            {% endfor %}
        </div>
    </div>
{% endblock %}
```
앞서 우리는 `posts/views.py`의 `main` 함수에서 `posts`라는 이름으로 모든 `Post` 객체를 보내주기로 했습니다.  
`Template`에서는 우리가 `view`에서 보낸 `posts`를 받아와 `for` 반복문을 통해 화면에 표시해주는 일을 할 거에요.  

`posts`에 담겨진 각각의 객체를 `post`로 하나씩 꺼내 반복문이 돌아간다고 생각하시면 됩니다.  
각각의 `post`의 속성에 접근하려면 `ORM`에서 사용하 방법처럼 점(`.`)을 사용하고, html에 변수가 표시되게 하려면 두 개의 중괄호(`{{ }}`)를 이용합니다.  

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
    </div>
</nav>
```
`new`로 연결하는 부분 위에 `posts`의 `main`으로 연결해주는 링크를 추가해 줄게요.  
