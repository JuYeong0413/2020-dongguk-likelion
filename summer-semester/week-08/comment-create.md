# 게시글에 댓글 작성하기    
## template
### posts/templates/posts/show.html  
페이지의 가장 아래쪽에 댓글을 입력하는 부분을 만들어주겠습니다.  
```html
<div class="container mt-5">
    <form class="form-row" action="#" method="POST">
      {% csrf_token %}
      <textarea class="form-control col-11 mr-2" name="content" placeholder="댓글 내용 입력..."></textarea>
      <input type="submit" class="btn btn-primary" value="확인">
    </form>
</div>
```
간단하게 `<textarea>`와 `<input type="submit">` 버튼을 넣어주었어요.  
`<form action="#">` 부분은 view와 url을 추가해 준 다음에 채워넣도록 하겠습니다.  

## view
### posts/views.py  
```python
from .models import Post, Comment

def create_comment(request, post_id):
    if request.method == "POST":
        post = get_object_or_404(Post, pk=post_id)
        current_user = request.user
        comment_content = request.POST.get('content')
        Comment.objects.create(content=comment_content, writer=current_user, post=post)
    return redirect('posts:show', post_id)
```
맨 위에 모델을 `import`해 오는 걸 잊지 마세요!  
위와 같이 `Post, Comment`를 직접 작성하셔도 되고, `from .models import *`와 같이 모든 모델을 `import`해 오게 해도 됩니다.  

게시글 상세보기, 수정, 삭제할 때 **어떤** 게시글에 대해서 작업을 수행할 것인지 알아야 하기 때문에 게시글 `id`값이 필요하다고 말씀드렸습니다.  
댓글도 마찬가지예요. **어떤 게시글에 달리는지** 알아야 정상적으로 댓글을 작성할 수 있겠죠? 그래서 `post_id`라는 변수에다가 우리가 게시글 `id`를 받아올거에요.  

html 파일에서 `<form method="POST">`로 지정해 주었으니, HTTP 요청 방식이 `POST`인 경우에만 댓글을 저장하도록 `if`문을 추가했습니다.  

`get_object_or_404`를 이용해서 `post`라는 변수에 댓글이 달릴 게시글을 담아두었습니다.  

`current_user`라는 변수에는 현재 요청을 보낸 사용자 즉, 댓글 작성자를 담아두었습니다.  

`comment_content`라는 변수에는 `POST` 방식으로 `name="content"`인 항목의 값을 가져와서 담아줬습니다. `<textarea>`에 작성된 내용이 담기겠죠?  

게시글을 생성할 때 사용한 `모델이름.objects.create`를 이용해서 댓글 객체를 생성합니다.  
:bulb: `Comment.objects.create(content=comment_content, writer=current_user, post=post)` 에서 `=`를 기준으로 왼쪽에 있는 `content`, `writer`, `post`는 모두 `Comment` 모델의 속성입니다. `=` 오른쪽에 있는 값은 해당 속성에 들어갈 변수 이름들입니다.  

마지막으로, `redirect`를 이용해서 댓글이 생성되면 상세보기 페이지로 돌아가서 댓글을 볼 수 있게 하려고 합니다.  
`app_name="posts"`인 앱의 `urls.py`에서 `name="show"`인 `path`로 찾아가는데, 상세보기 페이지를 띄우려면 어떤 게시글을 띄울 지 `id`가 필요하죠?  
요청에서 받아온 `post_id`를 같이 넘겨줍니다.  

:bulb: 여기서 `get_object_or_404`로 `post` 변수에 게시글이 담긴 상태인데, 왜 `post.id`나 `post.pk`가 아닌 `post_id`를 보내는지 의아해 하실 수 있을 것 같은데요, 그건 바로 들여쓰기에 주목해주시면 되겠습니다.  
`POST` 방식으로 들어온 요청은 `if` 문에 들어갔다가 나옵니다. 반면에 `POST` 이외의 요청은 `if`문에 들어가지 않은 채 바로 아래로 가게 됩니다. 여기서 `POST`로 요청한 경우가 아니더라도 게시글 상세보기 페이지로 다시 돌아가게 해 주기 위해서 `post_id`를 보내는 것입니다.  
만약에 `post.id`로 작성을 하면, `POST` 방식의 요청이 아닌 경우에는 `post`에 게시글이 담기질 않으니 `post.id`를 사용할 수 없는 상태가 되기 때문에 에러가 발생할거에요.  

## url  
### posts/urls.py  
```python
from django.urls import path
from .views import *

app_name="posts"
urlpatterns = [
    ...
    path('<int:post_id>/create_comment', create_comment, name="create_comment"),
]
```
`create_comment`라는 이름의 `url`을 만나면 `views.py`의 `create_comment` 함수로 `post_id` 값을 가지고 가는 `path`를 작성해 주었습니다.  
`view`에서 `def create_comment(request, post_id):` 라고 작성을 했기 때문에 `id`를 넘겨줄 때 이름을 맞춰 `<int:post_id>`로 작성해야 합니다.  

## template
### posts/templates/posts/show.html
```html
<div class="container mt-5">
    <form class="form-row" action="{% url 'posts:create_comment' post.id %}" method="POST">
      {% csrf_token %}
      <textarea class="form-control col-11 mr-2" name="content" placeholder="댓글 내용 입력..."></textarea>
      <input type="submit" class="btn btn-primary" value="확인">
    </form>
</div>
```
아까 전에 `#`으로 해 두었던 `<form action="#">` 부분을 바꿔줄 차례입니다.  
`path`의 이름을 `create_comment`라고 해 주었으니, `app_name`과 함께 `posts:create_comment`가 되고, 게시글 `id`도 보내줘야 하니까 `post.id`(또는 `post.pk`)를 뒤에 붙여 주었습니다.  

## admin
### posts/admin.py
```python
from .models import *

@admin.register(Comment)
class CommentAdmin(admin.ModelAdmin):
    list_display = (
        'id',
        'content',
    )
```
아직 댓글을 상세보기 페이지에서 함께 보여주고 있지 않으니, `admin.py`에 `Comment` 모델을 등록한 다음 댓글을 새로 작성해 보세요!  
