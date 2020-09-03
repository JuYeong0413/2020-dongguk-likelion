# 좋아요 모델  
지금까지 `Post`, `Comment`와 `User` 모델로 `1:N` 관계를 알아보았습니다. 이번에는 `M:N` 관계에 대해서 **좋아요** 기능을 구현하며 알아보겠습니다.  
만약 `M:N` 관계에 대한 내용이 기억나지 않는다면, [모델 관계 개념 복습자료](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/summer-semester/week-04/01-model-relations.md#mn-%EA%B4%80%EA%B3%84---manytomanyfield)를 통해 빠르게 복습하고 돌아오세요!  

좋아요 기능이 다대다 관계인 이유는 한 명의 사용자가 여러 게시글에 좋아요를 누를 수 있고, 하나의 게시글은 여러 명의 사용자로부터 좋아요를 받을 수 있기 때문입니다.  
그런데 이런 경우에 어떻게 모델링을 해야 할까요? 바로 다대다 관계를 가지는 두 모델(사용자, 게시글)에 대한 데이터를 저장할 모델을 추가로 만들어 주는 겁니다. 이를 중간(intermediate) 모델이라고 부릅니다.  

### posts/models.py  
```python
class Like(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        unique_together = (('user','post'))
```
자, 좋아요를 저장할 `Like` 모델을 만들어 주었습니다.  

어떤 사용자(`user`)가 어떤 게시글(`post`)에 좋아요를 눌렀는지 알아야 하기 때문에 두 모델에 대해서 `ForeignKey`로 연결이 되어있습니다.  

아래쪽에 `class Meta:` 부분이 생소하실 텐데요, 모델 클래스 내부에 `Meta` 클래스를 선언해서 모델에 메타데이터를 추가할 수 있습니다.  
모델 메타데이터란 모델 단위에서 설정할 수 있는 옵션이라고 볼 수 있습니다. 예를 들면 정렬이나 읽기 쉬운 이름, 복수형 이름 등이 있는데요, 다양한 메타 옵션이 궁금하시다면 [공식 문서](https://docs.djangoproject.com/en/3.0/ref/models/options/)를 읽어보시면 되겠습니다.  
참고로 메타 클래스를 선언할 때는 모델 필드 아래쪽에 한 줄 공백을 넣어주어야 합니다.

우리는 `unique_together`라는 옵션을 추가해 주었습니다. 이 옵션은 말 그대로 함께 유일해야 하는 필드의 쌍을 의미합니다.
`user`와 `post`를 넣어주었으니, `Like` 모델에서는 사용자와 게시글의 쌍이 고유하다는 의미가 되겠습니다.  
1번 사용자가 2번 게시글에 좋아요를 눌렀다는 정보는 중복으로 들어갈 수가 없으니까요.  

자, `Like` 모델을 만들었지만 여기서 끝이 아닙니다! `Post`에도 몇 가지 추가해야 할 내용이 있습니다.  
```python
class Post(models.Model):
    title = models.CharField(max_length=50, null=False)
    content = models.TextField()
    writer = models.ForeignKey(User, on_delete=models.CASCADE, null=True)
    view_count = models.IntegerField(default=0)
    image = models.ImageField(upload_to='images/', null=True)
    created_at = models.DateTimeField(auto_now_add = True)
    updated_at = models.DateTimeField(auto_now = True)
    like_user_set = models.ManyToManyField(User, blank=True, related_name="like_user_set", through="Like")
    
    @property
    def like_count(self):
        return self.like_user_set.count()
```
`like_user_set`이라는 속성과 `@property`가 달린 `like_count`라는 함수가 추가되었습니다.  

먼저 `like_user_set` 속성부터 살펴볼게요.  
`M:N` 관계를 나타내기 위해 `ManyToManyField`를 이용했습니다. `User` 모델과의 관계를 이루고 있고요, 게시글에 좋아요가 하나도 없을 수 있기 때문에 `blank=True`가 들어가 있습니다. `related_name`은 이전에 `Comment` 모델링 때 설명드렸으니 넘어가고, 마지막으로 `through`는 어느 모델을 통해서 관계가 정의되는지를 의미합니다. 지금은 `Like` 모델을 통해 `User`와 `Post`의 좋아요 관계가 정의되고 있습니다.  

`@property`는 데코레이터라고 부릅니다. 데코레이터는 다른 함수의 기능을 조작하여 새로운 함수를 만드는 일을 하는데, 중복을 줄여서 코드를 간결하게 해 주는 장점이 있습니다. 여기서 `@property`는 get method가 선언된다는 것을 표현합니다.  

게시글이 받은 좋아요의 개수를 반환하는 `like_count` 함수에서 `self`는 모델의 객체(인스턴스)를 의미합니다. 우리는 `Post`라는 모델에 대해서 좋아요 개수를 셀 것이 아니라, 각각의 게시글 객체의 좋아요 개수를 세야 한다는 점을 기억하시면 되겠습니다.  
`like_user_set`으로 게시글에 대해 좋아요를 누른 사용자의 목록을 가져왔으니, `count()` 함수를 이용해 개수를 세어 반환(`return`)하면 끝입니다.  

자, 이제 모델링이 끝났습니다.  
:bulb: `makemigration`과 `migrate`를 잊지 마세요!
