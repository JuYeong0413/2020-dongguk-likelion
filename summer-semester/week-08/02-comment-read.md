# 게시글에 작성된 댓글 보여주기  
작성된 댓글을 게시글의 상세보기 페이지에 띄워주겠습니다.  

## view  
### posts/views.py  
```python
def show(request, post_id):
    post = Post.objects.get(pk=post_id)
    post.view_count += 1
    post.save()
    all_comments = post.comments.all()
    return render(request, 'posts/show.html', {'post': post, 'comments': all_comments })
```
기존에 작성되어있는 게시글 상세보기 기능을 위한 `show` 함수를 조금만 수정하면 됩니다.  
`Comment` 모델링을 할 때, `post`에다가 `related_name="comments"`를 설정해 준 것 기억하시나요? 바로 그 `related_name`을 사용할 때가 되었습니다!  
위쪽에서 `post`라는 변수에 상세보기 페이지를 띄울 게시글이 담겨져 있는 상태죠. 그 게시글의 모든 댓글을 `all_comments`라는 변수에 담아주었습니다.  
그리고 `render`로 페이지를 띄울 때, 예전에는 게시글(`post`)만 가져갔지만, 이제는 댓글도 가져가야 하기 때문에 `comments`라는 이름으로 모든 댓글, `all_comments`를 보내줄거에요.  

## template  
### posts/templates/posts/show.html  
```html
<div class="container mt-5 mb-5">
    {% for comment in comments %}
        <div class="card">
            <div class="card-body">
                <b>{{ comment.writer }}</b> : {{ comment.content }}<br>
                <small>{{ comment.created_at }}</small>
            </div>
        </div>
    {% endfor %}
</div>
```
댓글 작성하는 부분 아래쪽에 댓글을 모두 표시해주겠습니다.  
게시글 목록을 보여준 페이지와 마찬가지로 `for`문을 이용해서 모든 댓글을 띄워줄거에요.  
`views.py`에서 `comments`에 모든 댓글을 담아서 보내주었으니, 하나의 댓글은 `comment`라는 변수에 담아서 하나씩 반복문을 돌며 표시해 주겠습니다. `Bootstrap`의 `card`를 이용할게요.  

댓글 작성자는 조금 진하게(bold) 표시하기 위해 `<b>` 태그로 감싸주었습니다. 댓글 내용을 표시한 다음에는 `<br>` 태그로 줄바꿈을 한 다음 댓글을 작성한 날짜를 보여주었어요.  
댓글이 작성된 날짜는 `<small>` 태그를 이용해서 조금 작게 표시되도록 했습니다.  

