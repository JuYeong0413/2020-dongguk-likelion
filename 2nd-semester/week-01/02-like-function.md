# 좋아요 기능  
모델링이 완료되었으니 본격적으로 기능 구현을 해 보겠습니다.  

## View  
### posts/views.py  
#### 방법 #1
```python
from django.contrib.auth.decorators import login_required

@login_required
def post_like(request, post_id):
    post = get_object_or_404(Post, pk=post_id)
    
    if request.user in post.like_user_set.all():
        post.like_user_set.remove(request.user)
    else:
        post.like_user_set.add(request.user)

    return redirect('posts:main')
```
본격적으로 함수를 설명하기에 앞서, 여기도 데코레이터가 있는 것을 확인할 수가 있습니다. 바로 `@login_required` 입니다.  
지금까지 우리는 `Template`에서 `{% if user.is_authenticated %}`으로 사용자의 로그인 여부를 확인했었습니다. `@login_required` 데코레이터를 이용하면 훨씬 편하게 사용자 인증 여부를 확인할 수가 있습니다.  

우선 `@login_required`를 사용하기 위해서는 `views.py` 상단에 `from django.contrib.auth.decorators import login_required`를 추가해야 합니다. 그리고 `View`에 접근하기 전에 사용자 인증 여부 확인이 필요하다면, `@login_required`를 붙여주면 됩니다. `@login_required` 데코레이터에 대한 자세한 설명은 [공식 문서](https://docs.djangoproject.com/en/3.0/topics/auth/default/#the-login-required-decorator)를 참고해 주세요.  

자, 이제 `post_like` 함수에 대해 알아볼게요.  
어떤 게시글에 좋아요를 누른 건지 알아야 하기 때문에 요청이 들어올 때 `post_id`라는 이름으로 게시글 `id`값을 받아올 겁니다. `get_object_or_404`를 이용해서 `Post` 모델에서 `pk`의 값이 `post_id`와 일치하는 객체를 찾아 `post`라는 변수에 담습니다.  

만약에 사용자가 좋아요를 누르면, 좋아요 취소도 가능해야겠죠? `post_like` 함수에서 모두 간편하게 처리할 수가 있습니다.  
특정 게시글 객체에서 좋아요를 누른 모든 사용자를 가져오기 위해서는 `like_user_set.all()`을 이용합니다. `post`라는 변수에 게시글 객체를 담아두었으니, `post.like_user_set.all()`이 되겠죠?  
포함 여부를 확인하는 `in` 연산자를 이용하면 좋아요를 누른 모든 사용자에 현재 요청을 보낸 사용자가 들어있는지 확인이 가능합니다. 이것이 바로 `if request.user in post.like_user_set.all()` 부분이 되겠습니다.  

만약 요청을 보낸 사용자가 좋아요를 누른 상태라면 좋아요를 취소해줘야겠죠? `ManyToManyField`에서는 `add`와 `remove` 메서드를 제공하고 있는데요, 좋아요 취소는 좋아요를 누른 사용자 목록에서 제거를 해야 하니까 `remove`를 사용했습니다. 반대로 좋아요를 누르지 않은 상태, 즉 `else` 부분으로 넘어가게 되면 새롭게 좋아요를 누른 것이기 때분에 `add`를 사용해 요청을 보낸 사용자를 추가해 줍니다.  

모든 작업이 끝난 이후에는 게시글 목록으로 `redirect` 시켜줄게요.  

#### 방법 #2  
```python
from django.contrib.auth.decorators import login_required

def post_like(request, post_id):
    post = get_object_or_404(Post,pk=post_id)
    post_like, post_like_created = post.like_set.get_or_create(user=request.user)

    if not post_like_created:
        post_like.delete()
        
    return redirect('posts:main')
```
좋아요 기능 구현을 하는 두 번째 방법! `get_or_create`를 이용하는 방법입니다.  
데코레이터에 대한 설명은 위에서 했으니 생략하고, 여기서도 마찬가지로 `post_id`를 이용해 해당 게시글 객체를 `post`라는 변수에 담아주었습니다.  

그 다음 줄에 주목해 주세요.  
```python
post_like, post_like_created = post.like_set.get_or_create(user=request.user)
```
우선 `post.like_set`은 `post`에 담겨있는 게시글 객체의 `Like`를 모두 가져옵니다. 그 다음 `get_or_create` 함수가 실행되는데요, `get_or_create`는 말 그대로 구하고자 하는 객체가 존재하면 가져오고 없으면 새로 생성하는 기능을 합니다.  
리턴 값으로 `(object, created)`의 튜플(tuple)을 생성합니다. 여기서 `object`는 얻고자 한 객체이고 `created`는 생성 여부를 나타내는 `boolean flag`입니다. 객체가 `get_or_create`에 의해서 생성되면 `created`는 `True`가 되고, 기존 데이터베이스에서 가져왔으면 `False`가 되는 것이죠.  
`get_or_create`가 두 개의 인자가 담긴 튜플을 반환하고 있기 때문에 `=`를 기준으로 왼쪽에 두 개의 변수(`post_like`, `post_like_created`)가 들어가게 됩니다.  

그래서 `not` 연산자를 이용해 `post_like_created`가 `False`라면, 즉 데이터베이스에서 객체를 가져와서 `post_like_created` 변수에 `False`값이 담긴다면 좋아요를 새로 만든 게 아니니까 기존에 존재하는 좋아요를 취소해줘야겠죠? `post_like` 변수에 담긴 좋아요 객체를 `delete()` 함수를 이용해서 삭제해 줍니다.  
만약 `post_like_created`에 `True`가 담긴다면, 즉 좋아요 객체가 새로 생성된다면 `if`문을 무시하게 되는 것이죠.  
:bulb: `not`이 붙어있다는 것에 유의하세요. `post_like_created`가 `False`이면 `not`을 만나 `True`가 되어 `if`문이 실행되는 것입니다.  

## URL  
### posts/urls.py  
```python
from django.urls import path
from .views import *

app_name="posts"
urlpatterns = [
    ...
    path('<int:post_id>/post_like', post_like, name="post_like"),
]
```
`path`를 작성하는 건 지금까지 계속 반복해서 설명을 했으니 생략하도록 하겠습니다.  

## Template  
좋아요 기능이 완성되었으니, 좋아요를 누를 버튼을 추가해줘야겠죠?  
[Font Awesome에 있는 무료 아이콘](https://fontawesome.com/icons?d=gallery&m=free)을 이용할 겁니다.  
[Free cheatsheet](https://fontawesome.com/cheatsheet)에서도 아이콘 확인이 가능합니다.  

### myblog/templates/base.html  
`Font Awesome`의 아이콘을 사용하기 위해서는 `Bootstrap`을 이용할 때와 마찬가지로 `CDN`을 html의 `<head>`부분에 추가해야 합니다.  
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.8.2/css/all.min.css"/>
```

이제 메인페이지와 게시글 상세보기 페이지에 좋아요 버튼을 넣어줄게요.  
### posts/templates/posts/main.html  
```html
<p>조회수 : {{ post.view_count }}</p>

<a href="{% url 'posts:post_like' post.id %}">
    {% if user in post.like_user_set.all %}
        <i class="fas fa-heart"></i>
    {% else %}
        <i class="far fa-heart"></i>
    {% endif %}
</a>

<a href="{% url 'posts:show' post.id %}" class="btn btn-primary">상세보기</a>
```
게시글 목록 페이지에서는 조회수와 상세보기 사이에 아이콘을 넣어주도록 하겠습니다.  
`<a>` 태그로 감싸서 좋아요 기능으로 넘어갈 수 있게 `url`을 설정해 주었고, `post.like_user_set.all`은 `View`를 작성하는 첫 번째 방법에서 설명드린 것처럼 특정 게시글에 좋아요를 누른 사용자를 모두 가져옵니다. `user`는 현재 사용자를 나타내고 있기 때문에 만약 사용자가 해당 게시글에 좋아요를 누른 상태이면 색깔이 채워져 있는 하트를, 좋아요를 누르지 않은 상태이면 색깔이 채워지지 않은 하트를 보여줍니다.  

### posts/templates/posts/show.html  
```html
<a href="{% url 'posts:post_like' post.id %}">
    {% if user in post.like_user_set.all %}
        <i class="fas fa-heart"></i>
    {% else %}
        <i class="far fa-heart"></i>
    {% endif %}
</a>
```
게시글 상세보기 페이지에서도 좋아요 버튼을 넣어주겠습니다.  

자 그런데 여기서 한 가지 아쉬운 부분이 생기게 됩니다. 바로 게시글 목록에서 좋아요를 누를 때와 게시글 상세보기 페이지에서 좋아요를 누를 때 모두 게시글 목록으로 가는 것입니다.  
게시글 상세보기에서 좋아요를 누르면 해당 게시글 상세보기 페이지로 다시 돌아갈 수 있게 하려면 어떻게 해야 할까요?  

예전에 [HTTP 메서드](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/summer-semester/week-02/01-HTTP-methods.md)에서 배운 `GET` 방식을 이용하면 가능합니다.  
`GET` 메서드는 **이름=값** 의 형태로 데이터를 전달할 수 있는데, url 맨 뒤에 `?`를 붙여서 데이터를 전달하면 됩니다.  

### posts/templates/posts/show.html  
```html
<a href="{% url 'posts:post_like' post.id %}?redirect_to=show">
    {% if user in post.like_user_set.all %}
        <i class="fas fa-heart"></i>
    {% else %}
        <i class="far fa-heart"></i>
    {% endif %}
</a>
```
차이가 보이시나요? `?redirect_to=show`라는 부분이 추가로 붙었습니다.  
기존에는 `post_like`라는 이름의 `path`로 `post.id`값을 전달했다면, 지금은 추가적으로 `redirect_to`라는 이름으로 `show`라는 값도 보내주게 되는 것입니다.  

## View  
### posts/views.py  
자 이제 `redirect`도 함께 수정해 줘야겠죠?  
```python
if request.GET.get('redirect_to') == 'show’:
    return redirect('posts:show', post_id)
else:
    return redirect('posts:main')
```
기존에 `return redirect('posts:main')` 부분을 위와 같이 변경해주면 됩니다.  
요청의 `GET` 방식으로 넘어온 `redirect_to`의 값이 `show`라면 `post_id`에 해당하는 게시글 상세보기 페이지로 넘어가고, `show`라는 값이 `redirect_to`에 담아서 오지 않았다면 게시글 목록 페이지로 가게 됩니다.  
