# Update 기능 구현  
Update는 Create와 유사합니다.  
## Template  
### posts/templates/posts/edit.html  
`posts/templates/posts/new.html` 파일의 코드를 복사해서 붙여넣어줍시다.  
그리고 글 수정 페이지에 알맞게 내용을 아래와 같이 고쳐줍시다.  
```html
{% extends 'base.html' %}

{% block content %}
<div class="container">
    <h1>글 수정하기</h1> <!-- edit 페이지니까 글 수정하기로 변경 -->
    <form action="{% url 'posts:update' %}" method="POST"> <!-- update 기능이니까 url 이름도 update로 해줍시다. -->
        {% csrf_token %}
        <div class="form-group">
            <label for="title">글 제목</label>
            <input type="text" class="form-control" name="title" id="title" placeholder="제목을 입력해주세요." value="{{ post.title }}" required> <!-- value 속성 추가 -->
        </div>
        <div class="form-group">
            <label for="content">글 내용</label>
            <textarea class="form-control" name="content" id="content" placeholder="내용을 입력해 주세요...">{{ post.content }}</textarea> <!-- 글 내용 추가 -->
        </div>
        <input type="submit" value="글 수정" class="btn btn-outline-primary"> <!-- 수정으로 버튼 변경 -->
    </form>
</div>
{% endblock %}
```
#### `new.html`과의 차이점  
- `<h1>` 태그 내용: `글 수정하기`  
- `<form>`의 `action`: `{% url 'posts:update' %}`  
`urls.py`와 `views.py`에서 `update`라는 이름을 사용할 것이기 때문에 미리 작성해 둘게요.  
- 글 제목 `<input>`: `value` 속성의 값으로 `{{ post.title }}` 추가  
- 글 내용 `<textarea>`: 여는 태그와 닫는 태그 사이에 `{{ post.content }}` 추가  
우리가 글 수정할 때 기존에 작성했던 내용을 불러와주는 게 편하잖아요? 그래서 내용과 제목 값을 넣어주는 작업이 필요합니다. `show.html`에서 템플릿 태그(`{{ }}`)로 내용을 표시해 준 것처럼, 글 수정 페이지에서도 템플릿 태그를 이용해서 값을 넣어줄 수 있습니다.  
- `submit` 버튼 이름: `글 수정`  

## View 
### posts/views.py  
```python
from django.shortcuts import render, redirect, get_object_or_404
...
def update(request, post_id):
    post = get_object_or_404(Post, pk=post_id)
    if request.method == "POST":
        post.title = request.POST['title']
        post.content = request.POST['content']
        post.save()
        return redirect('posts:show', post.id)
    return render(request, 'posts/edit.html', {"post": post})
```
#### get_object_or_404  
이번에 `get_object_or_404`라는 걸 사용해보도록 하겠습니다.  
상단에 `django.shortcuts`에서 `import`해 오는 부분에 `get_object_or_404`도 추가해주세요.  
`get_object_or_404`는 말 그대로 객체를 가져오거나 404 에러를 발생시킵니다.  
404 에러는 Not Found Error로, 페이지가 존재하지 않는다는 뜻입니다.  
`Post` 모델에서 `pk`가 인자로 받아온 `post_id`와 같은 객체를 찾는데, 해당 객체가 존재한다면 `post`라는 변수에 담을 것이고, 만약 해당 객체가 존재하지 않는다면 404 에러를 발생시키게 되는 것이죠.  

그 다음부터는 create 기능을 구현했을 때와 유사합니다. 만약에 요청된 방식이 `POST`라면 기존 글의 `title`과 `content`의 값을 사용자로부터 받아온 값으로 바꾼 다음 저장을 해 주는 것입니다.  
여기서 create와 다른 부분은  
```python
title = request.POST.get('title')
content = request.POST.get('content')
```
이 아니라  
```python
post.title = request.POST['title']
post.content = request.POST['content']
```
와 같이 대괄호(`[ ]`)를 사용한다는 점입니다.  
[딕셔너리(dictionary) 자료형에 대한 설명](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/summer-semester/week-02/02-python-dictionary.md)에서 말씀드린 것처럼, 딕셔너리에서 `key`에 해당하는 `value`값을 가져오는 방법에는 두 가지가 있는데요,  
1. `변수명.get('key')`, 즉 create에서 사용했던 `request.POST.get('title')`의 방법  
2. `변수명[key]`, 즉 `request.POST['title']`의 방법  
중에서 이번에는 두 번째 방법을 이용하는 것입니다.  

나중에 디버깅하는 방법을 알려드리겠지만, 수정을 하는 과정에서 `request.POST`의 값을 확인해보면  
```python
<QueryDict: {'csrfmiddlewaretoken': ['sIfEHZQfRd9fSRQC6Lznt5qHYPvnB0AGLtxhf0osGQewesNOY0tCrmsfKZ7NbFRb'], 'title': ['dongguk'], 'content': ['likelion2']}>
```
와 같이 `Python`의 딕셔너리 형태로 전달이 되는 것을 알 수 있습니다.  
그래서 앞서 설명드린 두 가지 방법을 이용할 수 있는 것이죠.  

그리고 새로운 글 객체를 만들 땐 `form`에서 넘어온 `title`과 `content` 값을 변수에 담은 다음 `create`를 했지만, 수정할 땐 우리가 수정할 글 객체를 가지고 있는 상태이기 때문에 바로 `.`을 이용해서 기존 글의 `title`과 `content` 값을 변경해 줍니다.  
마지막에 `save()`로 저장하는 것도 잊지 마세요!  

글 수정이 완료되면, 우리는 수정이 잘 되었는지 확인할 수 있게 `show`페이지로 가도록 하겠습니다. 상세보기를 할 때 글의 `id`가 필요하니, 앞서 `get_object_or_404`로 가져온 `post`의 `id`값을 같이 보내줘야 합니다.  

여기서 create와 한 가지 더 다른 점이 있습니다.  
우리는 글 작성 페이지를 띄워주는 `new`와 새로운 글 객체를 생성해주는 `create`, 이렇게 두 개의 함수로 create를 구현했습니다. 이번에는 페이지를 띄워주는 것과 글을 수정해주는 부분을 하나의 함수에서 모두 처리할 수 있게 구현해 보도록 하겠습니다.  

바로 `if`문의 특징을 이용하면 되는데요, [create 기능 구현 설명](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/summer-semester/week-02/04-create.md)에서 언급했던 부분이지만 다시 짧게 설명드리자면, `HTTP method`에는 다양한 방법이 존재합니다. 그 중에서 우리가 페이지를 띄울 땐 `GET` 방식을, 수정할 때는 `form`에서 `POST` 방식을 사용하죠.  
만약 `POST`방식을 사용한 요청이 들어온다면 `if`문에 들어가 되어있는 부분(수정)을 실행한 다음  
```python
return redirect('posts:show', post.id)
```
를 만나 `post.id` 값을 가지고 `show`라는 이름의 `url`을 거쳐 `views`의 `show`(`urls.py`에서 `show`는 `views.py`의 `show` 함수로 연결되기 때문)로 갑니다.  
그런데 우리는 `POST` 방식의 요청이 아닌 경우도 존재할 수 있기 때문에, `POST`가 아닌 다른 방식의 요청에 대해서는 `if`문을 거치지 않고 내려와  
```python
return render(request, 'posts/edit.html', {"post": post})
```
부분을 실행합니다. 즉, `post`를 들고 가서 `posts/edit.html`를 띄워주는 것이죠.  

이렇게 하나의 함수에서 페이지를 띄워주는 것과 특정한 작업을 수행하는 일 두 가지를 한번에 처리할 수 있습니다.  

## URL  
### urls.py  
```python
from django.urls import path
from .views import *

app_name="posts"
urlpatterns = [
    path('new/', new, name="new"),
    path('create/', create, name="create"),
    path('', main, name="main"),
    path('<int:post_id>/', show, name="show"),
    path('<int:post_id>/edit/', update, name="update"),
]
```
기존에 함수를 하나하나 `import`하는 것이 번거롭기 때문에 `*`로 모든 함수를 가져오도록 했습니다.  
그리고 우리가 만든 `update`에 대한 `path`를 작성해줄게요.  

### posts/templates/posts/show.html  
글 상세보기 페이지에서 수정된 시간과 수정할 수 있는 버튼을 추가해 줍시다.  
```html
{% extends 'base.html' %}

{% block content %}
{% include 'shared/_navbar.html' %}
<div class="container">
    <h1>글 #{{ post.id }} 상세보기</h1>
    <h3>글 제목 : {{ post.title }}</h3>
    <h3>글 내용 : {{ post.content }}</h3>
    <h3>작성 시각 : {{ post.created_at }}</h3>
    <h3>수정 시각 : {{ post.updated_at }}</h3>
    <a href="{% url 'posts:update' post.pk %}" class="btn btn-primary">수정</a>
</div>
{% endblock %}
```
작성했던 글 중에서 하나를 수정해보면, 잘 되는 것을 확인할 수 있습니다.  
