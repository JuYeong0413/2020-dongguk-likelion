# `pdb`를 이용한 디버깅  
우리가 코드를 작성할 때, 원인은 잘 모르겠지만 오류가 나는 경우가 셀 수 없이 많죠.  
그래서 효과적으로 디버깅을 할 수 있는 방법을 소개해드리려고 합니다.  

`Python`은 디버깅을 위해 `pdb`라는 `python debugger` 모듈을 제공합니다.  
`Django`에서 가장 기본적으로 사용할 수 있는데요,  
예를 들어 팔로우/팔로우 취소를 하는 과정에서 오류가 발생했다고 가정해보겠습니다.  

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
자, 이렇게 `views.py` 파일에서 `pdb`를 이용해서 디버깅을 하려면 우선 상단에  
```python
import pdb
```
를 추가해줍니다.  

그리고 나서 버그를 추적하기 위한 **중단점**을 `pdb.set_trace()`로 설정해주면 됩니다.  
```python
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth.decorators import login_required
from .models import *
import pdb

@login_required
def follow_toggle(request, user_id):
    user = request.user
    followed_user = get_object_or_404(User, pk=user_id)
    is_follower = user.profile in followed_user.profile.followers.all()
    
    pdb.set_trace() # 중단점
    
    if is_follower:
        user.profile.followings.remove(followed_user.profile)
    else:
        user.profile.followings.add(followed_user.profile)
        
    return redirect('home')
```
서버를 실행시킨 다음, 팔로우 하기/팔로우 취소 버튼을 눌러보세요.  
웹 브라우저의 페이지가 넘어가지 않고, 대기 상태로 인디케이터가 빙글빙글 돌아가는 것을 볼 수 있습니다.  
터미널을 한 번 확인해볼까요?  
```bash
(Pdb)
```
라는 게 표시되어 있을 겁니다.  
이 상태에서 `view`에 대한 디버깅을 진행하시면 됩니다.  
가령 `user`, `followed_user`, 아니면 `is_follower`를 입력해 변수의 값을 조회할 수도 있고 중단점 이후의 코드를 입력해 실행을 시켜보는 등 다양한 작업을 할 수 있습니다.  
:bulb: 디버거를 종료하려면 `q`(quit)를, 중단점 이후를 계속 실행시키려면 `c`(continue)를 누르면 됩니다.  
