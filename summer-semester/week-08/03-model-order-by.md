# 객체 정렬하기  
예전에 만들었던 게시글 목록 페이지도 그렇고, 조금 전 만들었던 게시글 상세보기 페이지에서 댓글 목록도 그렇고 공통점이 한 가지 있습니다.  
바로 오래된 객체부터 보여진다는 것인데요, **최신순**으로 정렬을 할 수 있는 방법을 알아보도록 하겠습니다.  

## view  
### posts/views.py  
```python
def main(request):
    posts = Post.objects.all().order_by('-created_at')
    return render(request, 'posts/main.html', {'posts': posts})
```
바로 `order_by`라는 함수를 사용하면 됩니다.  
`order_by`를 이용하면 정렬 조건을 설정할 수 있는데요, `-created_at`은 최신 생성순(내림차순)으로 정렬한다는 의미입니다.  
만약 오름차순 정렬을 원한다면 `-`을 뺀 `created_at`과 같이 사용하시면 되겠습니다.  

```python
def show(request, post_id):
   post = Post.objects.get(pk=post_id)
   post.view_count += 1
   post.save()
   all_comments = post.comments.all().order_by('-created_at')
   return render(request, 'posts/show.html', {'post': post, 'comments': all_comments })
```
모든 댓글을 가져오는 부분에도 동일하게 `order_by`를 붙여주었습니다.  

자, 이제 다시 확인해보면 게시글 목록도 댓글들도 모두 최신순으로 정렬된 것을 볼 수 있습니다.  
