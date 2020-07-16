# Media  
`media` 파일은 사용자가 업로드하는 파일을 의미합니다. 이전에 배운 [static 파일](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/summer-semester/week-01/03-static.md)은 프로젝트 안에 미리 준비되어 있는 상태로 있으면서 사용자에게 띄워주는 정적 파일과 다르게 언제 새로운 파일이 업로드 될 지 모른다는 특징이 있습니다.  

이번에는 media를 이용해서 사진을 업로드하는 방법에 대해 알아보겠습니다.  

### Pillow 패키지 설치  
우선, 이미지와 관련된 pip 패키지를 설치해야 합니다.  
```bash
$ pip install pillow
```
`Pillow` 패키지는 `Python` 이미지 라이브러리의 일종으로, 이미지 처리를 위해 사용됩니다.  
`width`, `height`, `format`, `resize` 작업 등이 가능합니다.  

### settings.py  
static 파일처럼 media 역시 `settings.py`에 경로 설정을 해야 합니다.  
```python
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'myblog', 'media')
```
static 설정과 유사한 모습인데요, `MEDIA_URL`은 웹페이지에서 사용할 media 파일의 최상위 URL 경로입니다. 실제 파일이나 디렉토리가 아니고 URL로만 존재하기 때문에 다르게 설정해도 무방하지만, 저는 `STATIC_URL`과 같은 형태를 유지하기 위해 `/media/`로 해 두겠습니다.  

`MEDIA_ROOT`는 사용자가 업로드한 파일을 어디에 모아둘지에 대한 경로를 의미합니다. `STATICFILES_DIRS`와 비슷한 역할이라고 생각하시면 됩니다. 마찬가지로 우리 프로젝트 폴더인 `myblog/` 폴더 안에 `media/` 폴더에서 media files를 관리하겠다는 의미입니다.  

### posts/models.py  
기존에 우리가 만들어 둔 `Post` 모델에는 이미지 속성이 없기 때문에 모델을 수정해야 합니다.  
```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=50, null=False)
    content = models.TextField()
    view_count = models.IntegerField(default=0)
    image = models.ImageField(upload_to='images/', null=True)
    created_at = models.DateTimeField(auto_now_add = True)
    updated_at = models.DateTimeField(auto_now = True)
```
`image`라는 이름의 속성을 추가해 주었습니다. 타입은 `ImageField`인데요, `upload_to` 속성으로 사용자가 올린 이미지를 어디에 저장할 것인지 지정해 주었습니다. `images/`라는 폴더 안에 저장되게 할 건데요, 우리가 `settings.py`에서 `MEDIA_ROOT`를 `myblog/media/`로 설정해 두었기 때문에 사용자가 업로드한 이미지가 저장될 정확한 경로는 `myblog/media/images/`가 되겠습니다.  

`image` 컬럼에 `null=True`도 포함되어 있는데요, 그 이유는 모델링을 할 때 기본 속성은 `null=False`이기 때문에 새로운 컬럼을 추가하면서 우리가 이미 기존에 만들어 둔 `Post`객체들에 대한 처리가 필요하기 때문입니다. (만약 `null=True`가 없는 상태로 `makemigrations`를 하면 현재 존재하는 객체에 대한 처리를 해야 한다는 문구가 터미널에 출력됩니다.)  
이미지는 필수 항목이 아님을 설정하기 위해 `null=True`를 넣어주도록 하겠습니다.  

모델을 수정했으니 해야할 일!  
```bash
$ python manage.py makemigrations
```
를 입력하면  
```bash
Migrations for 'posts':
  posts\migrations\0002_post_image.py
    - Add field image to post
```
라고 `posts`에 대한 `migration`의 목록으로 `image`라는 필드를 `post`에 추가했다고 나옵니다.  
궁금하신 분들은 `posts/migrations/0002_post_image.py` 파일을 열어서 한번 확인해 보시고,  
```bash
$ python manage.py migrate
```
`migrate`도 진행합시다.  
```bash
Running migrations:
  Applying posts.0002_post_image... OK
```
`OK` 가 표시되면 `migrate`가 성공적으로 완료된 것입니다.  

#### :bulb: 이미지 말고 파일을 저장하고 싶어요!
```python
file = models.FileField(upload_to='files/', null=True)
```
와 같이 작성하시면 됩니다. `FileField`는 파일을 저장하는 필드입니다.  

### myblog/urls.py  
media 파일은 static 파일과는 다르게 파일들을 불러와서 사용하기 위해서는 프로젝트의 `urls.py`에 경로를 지정해주어야 합니다.  
```python
...
from django.conf import settings
from django.conf.urls.static import static
...
urlpatterns = [
    ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```
우선 `settings`(프로젝트의 `settings.py`)를 `django.conf`에서, `static`을 `django.conf.urls.static`에서 `import`해야 합니다.  
그리고 `+` 연산자를 이용해서 기존의 `urlpatterns`에 추가하는 방식을 사용할 수도 있고  
```python
...
from django.conf import settings
from django.conf.urls.static import static
...
urlpatterns = [
    ...
]

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```
의 방식으로 추가를 해도 됩니다.  
:bulb: 참고: `a += b`는 `a = a + b`와 같은 의미입니다.  

### posts/templates/posts/new.html  
```html
{% extends 'base.html' %}

{% block content %}
<div class="container">
    <h1>새로운 글 작성하기</h1>
    <form action="{% url 'posts:create' %}" method="POST" enctype="multipart/form-data">
        {% csrf_token %}
        ...
        <div class="form-group">
            <label>이미지 첨부</label>
            <input type="file", class="form-control" name="image" accept="image/*">
        </div>
        <input type="submit" value="글 작성" class="btn btn-outline-primary">
    </form>
</div>
{% endblock %}
```
이미지를 업로드 할 수 있도록 `<input>`을 추가해 주었습니다.  
모델링을 할 때 우리가 `image`라는 이름의 속성으로 `Post` 모델을 수정해 주었으니, `name` 역시 `image`로 동일하게 해 줄게요.  
`accept="image/*"`는 타입이 이미지(jpg, png, ...)인 파일을 업로드 할 수 있게 하는 속성입니다. 사용자가 파일 선택을 누르면 이미지 파일을 보여줍니다.  

이미지를 `input`으로 처리할 때는 텍스트와 처리하는 방식이 다르기 때문에 인코딩을 해줘야 합니다. `<form>` 태그의 속성으로 `enctype="multipart/form-data"`를 반드시! 넣어주세요.  

### posts/templates/posts/edit.html  
`edit.html`에도 마찬가지로 `enctype`, `input`을 추가해줄게요.  
```html
{% extends 'base.html' %}

{% block content %}
<div class="container">
    <h1>글 수정하기</h1>
    <form action="{% url 'posts:update' post.pk %}" method="POST" enctype="multipart/form-data">
        {% csrf_token %}
        ...
        <div class="form-group">
            <label>이미지 첨부</label>
            <input type="file", class="form-control" name="image" accept="image/*">
        </div>
        <input type="submit" value="글 수정" class="btn btn-outline-primary">
    </form>
</div>
{% endblock %}
```

### posts/views.py  
이미지를 저장해줘야 하니 `create`와 `update` 함수를 수정해줍시다.  
```python
def create(request):
    if request.method == "POST":
        title = request.POST.get('title')
        content = request.POST.get('content')
        image = request.FILES.get('image')
        Post.objects.create(title=title, content=content, image=image)
    return redirect('posts:main')
```
```python
def update(request, post_id):
    post = get_object_or_404(Post, pk=post_id)
    if request.method == "POST":
        # pdb.set_trace()
        post.title = request.POST['title']
        post.content = request.POST['content']
        post.image = request.FILES.get('image')
        post.save()
        return redirect('posts:show', post.id)
    return render(request, 'posts/edit.html', {"post": post})
```
`file`에 대한 `input`은 파일 객체를 불러오는 `request.FILES.get()`을 이용해야 합니다.  

### posts/templates/posts/show.html  
글 상세보기에서 이미지도 띄워줘야겠죠?  
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
    {% if post.image %}
        <img src="{{ post.image.url }}" alt="사진">
    {% endif %}
    <a href="{% url 'posts:update' post.pk %}" class="btn btn-primary">수정</a>
    <a href="{% url 'posts:delete' post.pk %}" class="btn btn-danger">삭제</a>
</div>
{% endblock %}
```
글 중에서 이미지가 없는 글도 있기 때문에 `if`문을 이용합니다.  
이미지가 없는 글이라면 `if`와 `endif` 사이 부분이 없는 채로 화면이 표시될 것이고, 이미지가 있는 글이라면 이미지도 함께 표시될 겁니다.  

여기서 `<img>`의 `src`속성에 `{{ post.image }}`가 아니라 `{{ post.image.url }}`이 들어간다는 점을 유의해 주세요!  
