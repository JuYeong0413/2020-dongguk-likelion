# SNS
#### Original project source code from https://github.com/YJHoon/django-sns
## User에 Profile 만들기  
```bash
$ python manage.py startapp users
```
우선 모델링을 하기 전에 `users`라는 새로운 앱을 만들어 줍시다.  
그냥 `posts/models.py`에 해도 괜찮지만, `User`에 대한 서비스가 커지는 경우를 대비해서 나누어 관리를 해 주는 것이 좋기 때문에 앱을 만들어서 따로 모델링을 진행하도록 하겠습니다.  

### settings.py  
```python
INSTALLED_APPS = [
    ...
    'users',
]
```
앱을 새로 만들면 서버를 종료한 후 `settings.py`의 `INSTALLED_APPS`에 새로 만든 앱을 추가해 주는 것 잊지 않으셨죠?  

### users/models.py  
```python
from django.contrib.auth.models import User
from django.db import models

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    image = models.ImageField(verbose_name="Image of User", upload_to="image/", default="images/deafult.jpg")
    info = models.TextField(null=True, blank=True)
    followings = models.ManyToManyField("self", related_name="followers", symmetrical=False)
```
예전에 `Django`에서 기본적으로 제공하논 `User` 모델은 변경할 수 없기 때문에 새로운 속성을 추가하기 위해서는 `User` 모델과 1:1로 대응되는 다른 모델을 만들어야 한다고 말씀드렸는데요, `Profile` 모델이 바로 그 역할입니다.  
1:1 대응이니까 `OneToOneField`로 연결해주고, `User`가 삭제되면 해당하는 프로필도 함께 삭제되도록 `on_delete=models.CASCADE`를 설정해 주었습니다.  

`image`라는 프로필 사진을 저장할 속성도 만들어 주었습니다. 여기서 `verbose_name`이 생소하실텐데요, 사용자가 읽기 쉬운 모델 객체의 이름으로 간단하게 admin 페이지에 보여질 컬럼의 이름이라고 생각하시면 됩니다.  

사용자의 정보를 저장할 `info`라는 속성도 추가했습니다. `null=True`와 `blank=True` 옵션이 추가되어 있는데요, `null`은 존재하지 않는 값, `blank`는 비어있는 값(지금 상황에서는 `TextField`를 사용하고 있기 때문에 빈 문자열 `''`)을 의미합니다.  
완벽하게 일치하는 비유는 아니지만, 아래 사진을 참고하시면 `null`과 `blank`의 차이를 이해하기에 더 쉬울 겁니다.  

<img src="https://josjong.files.wordpress.com/2017/10/toilet-rolls.jpg?w=600">  

오른쪽이 `null` 입니다.

마지막으로, SNS의 핵심이라고 할 수 있죠. 팔로우/팔로잉에 대한 정보를 나타내는 속성인 `followings`를 추가해 주었습니다. 한 명의 유저는 많은 유저를 팔로잉 할 수 있고, 동시에 많은 유저가 한 명의 유저를 팔로우 할 수 있기 때문에 `M:N` 관계인 `ManyToManyField`를 사용합니다.  
`"self"`가 의미하는 것은 바로 자기 자신 클래스(모델)을 의미합니다. 우리는 `followings` 속성으로 프로필(사용자)과 프로필(사용자)의 관계를 나타낼 것이기 때문에 자기 자신 모델인 `Profile`을 의미하는 `"self"`를 넣어 준 것이죠.  

이제는 좀 익숙해진 `related_name`도 있습니다. 역참조, 역으로 추적하기 위한 이름이죠. 

초면인 `symmetrical=False`가 눈에 띌 겁니다. `Django`에서 `ManyToManyField`는 기본적으로 대칭이 되는 구조입니다. 이 말이 무슨 뜻이냐 하면 A라는 사용자가 B 사용자를 팔로우하면 B도 A를 팔로우하는, 맞팔 상태가 되는 구조라는 것입니다. 그런데 여러분 모두 아시다시피 SNS에서 A가 B를 팔로우한다고 해서 B도 A를 팔로우하면 안되잖아요? 그래서 `symmetrical=False`라는 속성을 추가해 주었습니다.  


:bulb: 팔로우? 팔로워? 팔로잉? 단어가 너무 헷갈려요.  
- 팔로우: '구독'의 의미입니다.  
- 팔로워: '나를' 구독(팔로우)하는 사람을 의미합니다.  
- 팔로잉: '내가' 구독(팔로우)하는 사람을 의미합니다.  
