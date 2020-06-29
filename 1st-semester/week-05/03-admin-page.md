# Django 모델을 admin에서 관리하기  
`Django` 서버를 실행시킨 다음  
```
http://localhost:8000/admin
```
위 주소로 들어가보면 로그인 창이 나옵니다.  

## superuser 만들기  
어드민 페이지에 로그인 할 수 있는 슈퍼유저를 만들어줘야 합니다.  
```bash
$ python manage.py createsuperuser
```
이름, 이메일, 비밀번호, 비밀번호 확인을 입력하면 슈퍼유저가 생성됩니다.  
비밀번호는 숫자+영문 8자 이상이어야 한다. 0000, 1234 같은 걸 입력하면 다시 입력하라고 나옵니다.  

다시 어드민 페이지에 들어가서 로그인을 해 보면 접속이 됩니다.  

## 모델을 admin에서 관리하기  
우리가 만들었던 `Post` 모델을 어드민 페이지에서 관리하려면 `admin.py` 파일에 등록을 해야 합니다.  
```python
from django.contrib import admin
from .models import Post

# Register your models here.
admin.site.register(Post)
```
다시 어드민 페이지에 들어가면 `Post`가 생긴 것을 확인할 수 있습니다.  

그런데 `Post` 목록이 조금 보기 불편합니다.  
이 목록은 우리가 원하는 대로 커스터마이징이 가능합니다.  

## admin 페이지 커스터마이징  
```python
from django.contrib import admin
from .models import Post

# Register your models here.
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = (
        'id',
        'title',
        'view_count',
        'created_at',
    )
    search_fields = (
        'title',
    )
```
- `@admin.register(Post)`: 함수나 클래스를 꾸며주는 역할을 하는 데코레이터입니다.  
- `list_display`: 목록에서 보여줄 컬럼(속성)을 설정하는 부분입니다. `Python`의 튜플(tuple) 형태로 작성합니다.  
- `search_fields`: 검색이 가능한 컬럼(속성)을 설정하는 부분입니다. 마찬가지로 튜플(tuple) 형태로 작성합니다.  

다시 어드민 페이지에 들어가보면, `Post` 목록에 `id`, `title`, `view_count`, `created_at` 속성 값이 보이는 걸 확인할 수 있습니다. 그리고 검색을 할 수 있는 부분이 위쪽에 추가되었습니다.  
어드민 페이지에서는 새로운 객체 생성, 수정, 삭제가 모두 가능합니다.  
