# Delete 기능 구현  
Delete는 매우 간단합니다.  

## View
### posts/views.py
```python
def delete(request, post_id):
    post = get_object_or_404(Post, pk=post_id)
    post.delete()
    return redirect('posts:main')
```
삭제하는 것 역시 글의 `id`가 필요합니다. 어떤 글을 삭제할건지 알려줘야 하기 때문이죠.  
`get_object_or_404`를 이용해서 `post_id`값이 `pk`와 일치하는 `Post` 객체를 찾아서 `post`라는 이름을 가진 변수에 담아줍니다.  
그리고 `delete()`를 이용하면 삭제됩니다. 끝!  
정말 간단하죠? 삭제한 다음에는 글 목록 페이지인 `posts`의 `main` url로 보내줍시다.  

## URL  
### posts/urls.py  
```python
...
urlpatterns = [
    ...
    path('<int:post_id>/delete/', delete, name="delete"),
]
```

### posts/templates/posts/show.html  
수정하기 버튼 옆에 삭제하기 버튼도 추가해줍시다.  
```html
<a href="{% url 'posts:delete' post.pk %}" class="btn btn-danger">삭제</a>
```

삭제를 하고자 하는 글의 상세보기 페이지에서 삭제 버튼을 누르면 글이 삭제된 후에 글 목록 페이지로 돌아가게 됩니다. 목록에서도 없어진 걸 확인할 수 있습니다.  

#### :bulb: 삭제를 위한 `template`을 안 만든 것 같은데요?    
우리가 새로운 글을 작성할 때와 수정할 때는 그에 따른 화면(`new.html`, `edit.html`이 필요했지만 삭제할 때는 사용자게에 따로 표시해 줄 화면이 없죠. 그래서 `template`이 없이 `views.py`와 `urls.py`만 작성해줘도 된답니다.  
