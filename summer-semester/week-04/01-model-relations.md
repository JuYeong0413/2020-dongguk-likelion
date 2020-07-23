# 모델 관계  
모델을 설계하는 데 있어서 모델 간의 관계는 빼놓을 수 없는 중요한 개념입니다.  
우리는 필요한 데이터의 내용의 형식에 따라 DB 내의 테이블들의 관계를 1:1, 1:N, 또는 M:N으로 적절히 구성해 줄 겁니다.  

## 1:1 관계 - `OneToOneField`  
1:1 관계의 예시로는 헬스장 회원과 락커를 생각할 수 있습니다.  
헬스장 회원 1명은 오직 하나의 락커만을 배정받아 사용할 수 있다고 할 때, 락커 또한 한 명의 회원에게만 이용됩니다. 서로 대응되는 게 유일하기 때문에 특정 회원이 어떤 락커를 사용하는지, 특정 락커를 이용하는 회원이 누구인지 바로 접근할 수 있다는 특징이 있습니다.  
```python
class Member(models.Model):
    name = models.CharField(max_length=10, null=False)
    gender = models.CharField(max_length=5)
    age = models.IntegerField()
    created_at = models.DateTimeField(auto_now_add = True)
    updated_at = models.DateTimeField(auto_now = True)
```
위와 같은 헬스장 회원 모델을 락커 모델에 1:1 관계로 연결하려면  
```python
class Locker(models.Model):
    ...
    member = models.OneToOneField(Member, on_delete=models.SET_NULL, blank=True, null=True)
```
와 같은 형식으로 작성을 하면 됩니다. 참고로, 두 모델 중 어느 쪽에서 `OneToOneField`를 선언해도 무방합니다.  

1:1 관계인 모델에 접근하는 방법은  
```python
>>> from members.models import Member
>>> member = Member.objects.get(pk=1)
>>> member.locker
```
와 같이 특정 회원의 락커를 확인하거나  
```python
>>> from lockers.models import Locker
>>> locker = Locker.objects.get(pk=1)
>>> locker.member
```
와 같이 특정 락커를 이용하는 회원을 확인할 수 있습니다.  

## 1:N 관계 - `ForeignKey`  
1:N 관계의 예시로는 우리가 학습한 게시글과 댓글을 생각할 수 있습니다.  
하나의 글에는 여러 개의 댓글이 존재한다는 점을 생각하시면 됩니다.  
`ForeignKey`를 N쪽에 해당하는 모델에 정의하며, 만약 자신을 참조하게 되는 경우에는 대상 클래스명에 `self`라고 적으면 됩니다.  
```python
class Post(models.Model):
    title = models.CharField(max_length=50, null=False)
    content = models.TextField()
    view_count = models.IntegerField(default=0)
    image = models.ImageField(upload_to='images/', null=True)
    created_at = models.DateTimeField(auto_now_add = True)
    updated_at = models.DateTimeField(auto_now = True)
```
이렇게 우리가 만들어 둔 `Post` 모델에  
```python
class Comment(models.Model):
    ...
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
```
위와 같은 `Comment` 모델을 만들어 주었습니다.  

여기서 `on_delete` 속성에 대해서 한번 알아봅시다. 
`on_delete`는 1인 쪽의 데이터가 삭제될 경우, N쪽의 데이터를 어떻게 처리할 것인지에 대해 정의하는 부분입니다.  
- `CASCADE`: 1인 쪽(게시글)과 연결되어 있는 N쪽(댓글)의 모든 데이터 삭제  
- `PROTECT`: 1인 쪽(게시글)의 데이터가 삭제되지 않도록 보호  
- `SET_NULL`: `null`로 값을 대체합니다. 필드에 `null=True` 옵션이 있어야만 가능합니다.  
- `SET_DEFAULT`: `default`값으로 대체합니다. 필드에 `default`값이 설정되어 있어야 합니다.  
- `SET()`: 대체할 값이나 함수를 괄호 안에 지정합니다.  
- `DO_NOTHING`: 아무런 작업을 수행하지 않습니다. 하지만 DB에서 오류가 발생할 수 있습니다.  

1:N 관계인 모델에 접근하는 방법은  
```python
>>> from comments.models import Comment

>>> comment = Comment.objects.get(pk=1)
>>> comment.post
```
와 같이 특정 댓글이 어느 게시글에 작성된 것인지 확인하거나  
```python
>>> from posts.models import Post

>>> post = Post.objects.get(pk=1)
>>> post.comment_set
>>> post.comment_set.all()
>>> post.comment_Set.count()
```
와 같이 특정 게시글의 댓글을 모두 확인할 수 있습니다.  

N(댓글) 쪽에서는 관계되어 있는 모델이 1(게시글)이므로 바로 접근이 가능하지만, 1(게시글) 쪽에서는 관계되어 있는 객체(댓글)가 많기 때문에 `set`을 통해서 접근할 수 있습니다.  

## M:N - `ManyToManyField`  
WIP
