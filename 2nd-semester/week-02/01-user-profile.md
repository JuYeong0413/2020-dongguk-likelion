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

## Signal  
자, 이렇게 `User` 모델에 대응하는 `Profile`을 만들었는데요, `OneToOneField`로 관계를 지정해주었다고 해서 새로운 `User` 객체가 생길 때(새로운 사용자가 가입했을 때) 자동적으로 `Profile` 객체가 생성되지 않습니다. 우리가 직접 `Profile` 객체를 만들어야 하는데요, 이 작업을 일일이 하면 정말 번거롭겠죠?  
`Django`에서는 특정한 일(저장하기 전, 저장한 후, 삭제한 후, ...)을 수행할 때 동작을 지정해서 자동적으로 수행할 수 있는 기능을 가지고 있습니다. 그 기능을 **`signal`(신호)** 라고 합니다. `signal`에 대한 자세한 설명은 [공식 문서](https://docs.djangoproject.com/en/3.0/topics/signals/)를 확인해주시면 되겠습니다.  

우리가 지금 필요한 `signal`은 `User` 모델이 새로 생성되어 저장이 된 이후에 1:1로 대응되는 `Profile` 객체를 생성해 주는 것이기 때문에 저장한 후에 동작을 하는 `signal`을 사용할 겁니다. 그 `signal`이 바로 `post_save`입니다.
:bulb: `post_save`에서 `post`는 이후라는 의미를 가지고 있는 영어단어 `post`입니다. 우리가 이전에 만들었던 블로그 프로젝트에서의 게시글(post)이 아닙니다. HTTP 메서드 중에서 `POST` 방식을 의미하는 것도 아닙니다!  
`signal`을 사용하기 위해서는 `receiver` 데코레이터를 이용해야 하는데요,

```python
from django.contrib.auth.models import User
from django.db import models
from django.dispatch import receiver
from django.db.models.signals import post_save
```
`models.py` 상단에 이렇게 `receiver`와 사용할 `signal`을 `import`해야 합니다.  

`signal`을 이용해서 선언한 함수는 `receiver function`이라고 부릅니다.  
먼저 함수를 작성한 다음 차근차근 설명을 드리도록 하겠습니다.  

```python
@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)

@receiver(post_save, sender=User)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()
```
우선 `receiver`는 `sender`와 `keyword argument`를 받아야 하는 조건이 있습니다.  
`sender`는 어떤 이벤트가 발생했을 때, 해당 이벤트가 발생했음을 시스템에 알려주는 주체입니다. 기본적으로 `Django`에 내장된 모델 관련 `signal`이 있고, 이러한 `signal`은 `Model` 클래스를 `sender`로 해서 작동합니다. 간단하게 말하면, 지금 상황에서 `sender`는 모델이 되는 겁니다.  
모델 중에서도 `User` 모델과 관련해서 `save`가 작동한 이후에, 즉 `post_save`가 되면 위에 작성한 `receiver function`이 실행됩니다.  

그럼 첫 번째 `create_user_profile`을 먼저 살펴볼게요.  
`sender`는 위에 `receiver`에서 설정한 `sender=User`에서 알 수 있듯이 `User` 모델이 됩니다. `instance`는 가입하거나 변경된 `User`객체가 되고, `created`에는 새롭게 생성된(가입한) 사용자인지 `True` 또는 `False`값이 넘어옵니다.  
`instance`에 왜 가입한 객체가 아니라 변경된 객체도 포함되나요:question: 그 이유는 바로 `signal`이 `post_save`이기 때문입니다. 저장된 이후이기 때문에, 생성된 다음 저장이 되었을 때랑 수정된 다음 저장이 되었을 때 모두 호출이 되는 것입니다.  
마지막에 `**kwargs`에서 `kwargs`는 `keyword arguments`의 약자입니다. 위에서 `receiver`는 `sender`와 `keyword argument`를 받아야 하는 조건이 있다고 말씀드린 거 기억하시죠? 그래서 추가되는 부분인데요, `키워드 = 값`의 형태로 함수를 호출할 수 있으며 결과값이 딕셔너리 형태로 출력됩니다.  
간단한 예시를 살펴보자면  
```python
# -*- coding utf-8 -*-
def profile(**kwargs):
    print(kwargs)
```
`profile`이라는 함수를 만들어서
```python
profile(name1="dongguk", name2="likelion")
```
위와 같이 실행해보면
```python
{'name1': 'dongguk', 'name2': 'likelion'}
```
이 출력되는 것을 확인할 수 있습니다.

`**kwargs` 말고 `argument`의 약자인 `*args`도 있는데, 추가로 설명을 하면 여러분이 힘드실테니 `*args`에 대해 자세하게 알고 싶으신 분들은 직접 찾아보시기 바랍니다.  

자, 이제 모델링이 완료되었으니 해야할 것은?
```bash
python manage.py makemigrations
python manage.py migrate
```
