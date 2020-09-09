# Follow 기능 추가  
### templates/home.html  
사용자를 팔로우하려면 팔로우 버튼이 필요하겠죠. 하나 만들어 줍시다.  
```html
<div class="follow-btn">
    {% if user != post.user %}
        {% if user.profile in post.user.profile.followers.all %}
            <a href="#" class="btn btn-primary">팔로우 취소</a>
        {% else %}
            <a href="#" class="btn btn-outline-primary">팔로우 하기</a>
        {% endif %}
    {% endif %}
</div>
```
`url`이 연결되는 `href` 부분은 잠시 `#`처리를 해 두고, 버튼을 추가해 주었습니다.  

### users/views.py  
```python
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth.decorators import login_required
from .models import *

@login_required
def follow_toggle(request, user_id):
    user = request.user
    followed_user = get_object_or_404(User, pk=user_id)
    is_follower = user.profile in followed_user.profile.followers.all()
    
    if is_follower:
        user.profile.followings.remove(followed_user.profile)
    else:
        user.profile.followings.add(followed_user.profile)
        
    return redirect('home')
```
팔로우 기능은 [좋아요 기능](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/2nd-semester/week-01/02-like-function.md#%EB%B0%A9%EB%B2%95-1)을 구현한 방법과 동일합니다.  

우선 요청을 보낸 사용자를 `user`라는 변수에 담아주었습니다.  
그리고 게시글의 작성자 `id`를 `user_id`라는 변수에 받아올건데, 이걸 이용해서 게시글 작성자 객체를 `followed_user`라는 변수에 담아주도록 하겠습니다.  
게시글의 작성자를 팔로우하는 모든 사용자를 가져오기 위해서 `followed_user.profile.followers.all()`을 이용했는데요, 만약에 현재 요청을 보낸 사용자(`user` 변수에 담긴 사용자)가 포함되어 있으면(팔로우 하는 상태라면) `is_follower`라는 변수의 값은 `True`, 포함되어 있지 않으면(팔로우하지 않는 상태라면) `is_follower`라는 변수의 값은 `False`가 들어가게 됩니다.  

팔로우하고 있는 상태라면, 팔로우 취소를 해야겠죠? `remove()` 함수를 이용해서 요청을 보낸 사용자가 팔로우하는 사람 목록에서 게시글 작성자를 삭제해줍니다.  
팔로우하고 있지 않는 상태라면, 새롭게 팔로우를 하는 것이기 때문에 `add()` 함수를 이용해서 요청을 보낸 사용자가 팔로우하는 사람 목록에서 게시글 작성자를 추가해줍니다.  

조금 헷갈린다고요? 팔로워, 팔로잉 개념을 다시 생각하면서 코드를 천천히 읽어보세요.  
- 팔로워: '나를' 팔로우 하는 사람
- 팔로잉: '내가' 팔로우 하는 사람

- `followed_user.profile.followers.all()`: `followed_user`(게시글 작성자)의 팔로워, 즉 게시글 작성자를 팔로우 하는 사람  
- `user.profile.followings`: `user`(요청을 보낸 사용자)가 팔로우 하는 사람  

### users/urls.py  
```python
from django.urls import path
from .views import *

app_name="users"
urlpatterns = [
    path('<int:user_id>'/follow_toggle/', follow_toggle, name="follow_toggle"),
]
`path` 작성하는 방법은 이제 더 이상 설명하지 않아도 이해하시죠?  

### django_sns/urls.py  
```python
urlpatterns = [
    ...
    path('users/', include('users.urls')),
]
```
대신 `users` 앱을 우리가 새로 만들어주었으니, 프로젝트의 `urls.py` 파일에 `include`해 주는 것 잊지 맙시다.  

### templates/home.html  
마지막으로 `#` 처리를 해 두었던 팔로우 하기, 팔로우 취소 버튼에 `url`을 연결해주면 완성!    
```html
<div class="follow-btn">
    {% if user != post.user %}
        {% if user.profile in post.user.profile.followers.all %}
            <a href="{% url 'users:follow_toggle' post.user.id %}" class="btn btn-primary">팔로우 취소</a>
        {% else %}
            <a href="{% url 'users:follow_toggle' post.user.id %}" class="btn btn-outline-primary">팔로우 하기</a>
        {% endif %}
    {% endif %}
</div>
```
