# Django 모델링  
앱 폴더 내부의 `models.py` 파일에서 진행합니다.  
예를 들어, `posts` 앱에서 게시글에 대한 모델 `Post`를 만든다고 가정해 봅시다.  
```python
from django.db import models

class Post(models.Model):
    ## 속성이 들어갈 자리
```
`Django`에서 `model`은 `class` 개념, `models` 패키지의 `Model`을 상속받습니다.  
모델링을 할 땐, 해당 모델에 어떤 속성이 있는지 먼저 정의한 다음 각각의 속성에 대한 데이터 타입을 결정하면 됩니다.  
게시글에는 제목, 내용, 조회수 등이 포함되겠죠.  
```python
from django.db import models

class Post(models.Model):
    title
    content
    view_count
    ...
```
이제 각 속성에 대한 데이터 타입을 정해봅시다.  
```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=50, null=False)
    content = models.TextField()
    view_count = models.IntegerField(default = 0)
```
`Django`의 모델에는 다양한 데이터 타입이 존재합니다. 자세한 타입 설명은 [documentation](https://docs.djangoproject.com/en/3.0/ref/models/fields/#field-types)을 확인해 주세요.  
지금은 가장 자주 사용하는 몇 가지의 데이터 타입에 대해서 알려드리겠습니다.  
- `CharField`: 문자열을 위한 필드입니다.  
`max_length` 속성을 필수로 지정해 주어야 하는데, 문자열의 최대 길이를 나타냅니다.  
추가적으로 게시글을 작성할 때, 제목이 비어있지 않도록 `null=False`를 추가해 주었습니다. (속성은 기본적으로 `null=True`입니다.)  
- `TextField`: 긴 문자열(주로 `textfield` 사용 시)을 위한 필드입니다.  
- `IntegerField`: 정수형 타입입니다. -2147483648~2147483647 사이의 숫자가 저장될 수 있습니다.  
조회수의 경우, 초기값은 0이 되어야 하기 때문에 `default`속성을 추가해 0으로 초기화 했습니다.  

그리고 `Django`에서 모델링을 할 때 중요한 속성이 있습니다.  
- `created_at`: 객체가 언제 생성되었는지를 나타내는 속성입니다.  
- `updated_at`: 객체가 언제 수정되었는지를 나타내는 속성입니다.  

위 두 가지 속성은 반드시 모델에 추가해야 합니다.  
```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=50, null=False)
    content = models.TextField()
    view_count = models.IntegerField(default = 0)
    created_at = models.DateTimeField(auto_now_add = True)
    updated_at = models.DateTimeField(auto_now = True)
```
`created_at`과 `update_at`은 둘 다 `DateTimeField` 타입입니다.  
`DateTimeField`는 날짜와 시간을 저장하는 데이터 타입입니다.  
그런데 우리가 객체가 생성되고 수정될 때 일일이 현재 날짜와 시간을 넣는 것은 굉장히 번거로운 일이죠.  
그래서 `auto_now_add`와 `auto_now` 속성을 이용할 겁니다.  
- `auto_now_add`: 생성될 때 현재시간을 자동으로 저장하는 속성입니다.  
- `auto_now`: 생성, 수정될 때 현재시간을 자동적으로 저장하는 속성입니다.  

생성된 시간을 저장하는 `created_at` 필드는 수정될 때 값이 업데이트 될 필요가 없기 때문에 `auto_now_add=True`로 지정하고,  
수정된 시간을 저장하는 `updated_at`필드는 수정될 때마다 값이 업데이트되어야 하기 때문에 `auto_add=True`로 지정합니다.  
참고로 처음에 객체가 생성되면 `updated_at`의 값은 `created_at`과 동일하게 됩니다.  

## migration  
모델을 만들었으니 이제 `migration`을 진행해야 합니다.  
```bash
$ python manage.py makemigrations
```
위 명령어를 실행하면 `migrations/` 디렉토리에 파일이 생성됩니다.  
```bash
$ python manage.py migrate
```
위 명령어는 `makemigrations`의 내역대로 테이블을 생성합니다.  
