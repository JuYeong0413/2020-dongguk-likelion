# User 모델  
`User` 모델은 `Django`에서 기본적으로 제공하는 사용자 관련 모델입니다.  
`username`, `password`, `email` 등의 속성을 가지고 있습니다.  

`User` 모델은 변경할 수 없기 때문에 `User` 모델과 1:1로 대응되는 다른 모델을 만들어서 속성을 추가해 주어야 합니다. `User` 모델을 상속받는 방법을 사용해도 됩니다.  

## Model  
### posts/models.py  
```python
from django.db import models
from django.contrib.auth.models import User

class Post(models.Model):
    title = models.CharField(max_length=50, null=False)
    content = models.TextField()
    writer = models.ForeignKey(User, on_delete=models.CASCADE, null=True)
    view_count = models.IntegerField(default=0)
    image = models.ImageField(upload_to='images/', null=True)
    created_at = models.DateTimeField(auto_now_add = True)
    updated_at = models.DateTimeField(auto_now = True)
```
`User` 모델을 사용하기 위해 `django.contrib.auth.models`에서 `User`를 `import` 했습니다.  

그리고 사용자와 게시글의 관계를 생각했을 때, 한 명의 사용자가 여러 개의 게시글을 작성할 수 있기 때문에 1:N 관계입니다. N쪽에다가 `ForeignKey`를 추가해야 하므로 `Post`모델에 `User`모델을 `ForeignKey`로 연결해서 작성자(`writer`)라는 속성을 추가해 주었습니다.  
우리가 `User` 모델을 사용하기 이전에 작성한 게시글은 작성자가 존재하지 않는 상태이기 때문에 `null=True` 옵션을 추가해 주었습니다.  

추후에 배울 댓글(`Comment`)에 대한 모델링도 아래쪽에 미리 진행해두도록 하겠습니다.  
댓글 역시 사용자와 게시글의 관계처럼, 하나의 게시글에는 여러 개의 댓글이 달릴 수 있는 1:N의 관계입니다.  
N쪽인 `Comment`에 `ForeignKey`를 추가해 주었습니다.  
```python
class Comment(models.Model):
    content = models.TextField()
    writer = models.ForeignKey(User, on_delete=models.CASCADE)
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    created_at = models.DateTimeField(auto_now_add = True)
    updated_at = models.DateTimeField(auto_now = True)
```
모델을 수정했으니 `makemigrations`와 `migrate`을 해줍니다.  
```bash
$ python manage.py makemigrations
```
```bash
$ python manage.py migrate
```

## View  
### posts/views.py  
`Post` 모델에 작성자 속성이 추가되었으니, `create`함수에도 작성자를 저장하도록 추가해줍시다.  
```python
def create(request):
    if request.method == "POST":
        title = request.POST.get('title')
        content = request.POST.get('content')
        writer = request.user
        image = request.FILES.get('image')
        Post.objects.create(title=title, content=content, writer=writer, image=image)
    return redirect('posts:main')
```
`request.user`는 요청을 보낸 사용자에 대한 정보를 담고 있습니다.  

## Template  
### posts/templates/posts/show.html  
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
`User`를 추가하기 전에 작성된 게시글의 작성자는 알 수 없기 때문에 `if`문을 이용해서 게시글 작성자가 존재하면 작성자 정보를, 존재하지 않는다면 `알 수 없음`이 표시되게 했습니다.  

그리고 다른 사람이 내가 작성한 글 수정과 삭제를 할 수 있는 권한을 주면 안 되기 때문에 현재 사용자(`user`)가 `post`의 `writer`인지 확인해서 버튼이 보여지게 수정했습니다.  
