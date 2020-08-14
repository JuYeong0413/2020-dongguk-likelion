# Comment 모델링  
우리가 만든 블로그에 댓글을 작성하는 기능을 추가해봅시다.  
본격적으로 기능 추가에 앞서, 모델링을 진행해 볼게요.  

### posts/models.py  
```python
class Comment(models.Model):
    content = models.TextField()
    writer = models.ForeignKey(User, on_delete=models.CASCADE)
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    created_at = models.DateTimeField(auto_now_add = True)
    updated_at = models.DateTimeField(auto_now = True)
```
`Post` 모델을 작성한 부분 아래쪽에 코드를 추가해줍시다. (2줄 띄우는 것을 잊지 마세요!)  

댓글(`Comment`) 모델에는 다음과 같은 속성이 있습니다.  
- 내용(`content`): 내용은 글자수가 많을 수 있으니 `TextField`를 사용하게 했습니다.  
- 작성자(`writer`): 한 명의 유저는 여러 개의 댓글을 남길 수 있기 때문에 `1:N` 관계입니다. 따라서 `User`(사용자) 모델과 `ForeignKey` 관계를 설정해 주었습니다.  
`on_delete` 속성에 대한 설명은 모델 관계 개념을 설명할 때 진행한 부분이므로 생략하겠습니다. 만약 기억이 안 난다면 [이곳](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/summer-semester/week-04/01-model-relations.md)을 클릭해 해당 복습자료를 다시 확인해 주시기 바랍니다.  
- 게시글(`post`): 하나의 게시글에는 여러 개의 댓글이 달릴 수 있기 때문에 `1:N` 관계입니다. 따라서 `Post`(게시글) 모델과 `ForeignKey` 관계를 설정해 주었습니다.  

  여기서 주목해야 할 부분은 `related_name='comments'` 인데요, `related_name`이라는 속성은 역으로 추적을 할 때 사용됩니다.  
[모델 관계 개념](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/summer-semester/week-04/01-model-relations.md)을 설명할 때 `1:N` 관계에서 1 쪽에서 N에 접근하기 위해서는 `set`을 통해서 접근한다고 말씀드렸는데요, 이 `set`과 같은 기능입니다.  
`set`은 `Django`에서 기본적으로 제공을 해 주는 것이지만 만약에 우리가 `related_name`을 설정해 준다면 `set`을 대신해서 사용할 수 있게 되는 거죠.  
  ```python
  >>> from posts.models import Post

  >>> post = Post.objects.get(pk=1)
  >>> post.comments
  ```
  와 같이 사용할 수 있게 되는 겁니다. `set`보다 좀 더 직관적이죠?  

  :bulb: 참고로 `ForeignKey`의 `related_name`은 N쪽을 접근하기 위한 이름이기 때문에 복수의 객체를 반환합니다. 따라서 복수형으로 이름을 지정하는 것이 적절합니다.  
- 생성된 시간(`created_at`)  
- 수정된 시간(`updated_at`)  
생성된 시간, 수정된 시간에 대한 설명은 모델링을 처음 배울 때 설명한 부분이기 때문에 생략하겠습니다. 만약 기억이 안 난다면 [이곳](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/1st-semester/week-05/01-modeling.md)을 참고해 주세요.  
