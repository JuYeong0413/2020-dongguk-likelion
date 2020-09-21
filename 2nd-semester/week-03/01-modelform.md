# ModelForm  
`ModelForm`은 `form` 클래스를 상속받은 클래스입니다.  
```python
<form action="{% url 'posts:create' %}" method="POST">
    {% csrf_token %}
    <div class="form-group">
        <label for="title">글 제목</label>
        <input type="text" class="form-control" name="title" id="title" placeholder="제목을 입력해주세요." required>
    </div>
    <div class="form-group">
        <label for="content">글 내용</label>
        <textarea class="form-control" name="content" id="content" placeholder="내용을 입력해 주세요..."></textarea>
    </div>
    <input type="submit" value="글 작성" class="btn btn-outline-primary">
</form>
```
우리가 지금까지 작성한 일반 `form`은 위와 같이 직접 필드를 정의해야 했었죠.  
하지만 `ModelForm`을 사용한다면 모델 맞춤형 `form`이 구성되고, 보다 편리하게 저장할 수 있게 됩니다.  

## Form  
### posts/forms.py  
먼저, 기반으로 할 `models.py` 파일과 동일한 경로에 `forms.py` 파일을 만들어주세요.  
#### ModelForm 클래스 방식으로 정의하는 방법  
```python
from django import forms
from .models import *

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content']
```
`form` 클래스를 사용해야 하기 때문에 `import`하고, `models.py`에 모델이 정의되어 있으니 모델도 `import`해주겠습니다.  
그리고 아래에 클래스를 정의해주면 됩니다.  

우리는 모델을 기반으로 하는 클래스를 만들어야 하기 때문에 `forms` 클래스의 `ModelForm`을 인자로 넘겨줍니다.  
```python
from django.forms import ModelForm
from .models import *

class PostForm(ModelForm):
    ...
```
`ModelForm` 자체를 `import`해도 됩니다.  

`ModelForm`을 사용할 때는 기초가 되는 `model`, 여기서는 `Post` 모델과 `form`에서 입력받을 필드(`fields`)만 `Meta` 클래스에 정의하면 됩니다.  
지금은 `Post` 클래스를 모델로 하고, 제목인 `title`과 내용인 `content`만 입력받도록 `fields`에 넣어주었어요.  

만약 `Post` 모델에 정의된 모든 필드를 `form`에 포함시키고 싶다면,  
```python
class PostForm(ModelForm):
    class Meta:
        model = Post
        fields = '__all__'
```
라고 하면 됩니다.  

특정 필드만 제외하고 나머지를 `form`에 포함하려면
```python
class PostForm(ModelForm):
    class Meta:
        model = Post
        exclude = ['user']
```
와 같이 `exclude` 속성으로 지정해주면 됩니다.  

#### modelform_factory 함수 방식으로 정의하는 방법  
```python
from django.forms.models import modelform_factory
from .models import *

PostForm = modelform_factory(Post, fields='__all__')
```
`modelform_factory` 함수는 모델을 베이스로 한 `ModelForm` 클래스를 만들어서 리턴합니다. `ModelForm`의 `Meta` 클래스로 지정하는 `fields`나 `exclude` 항목 중 하나를 반드시 지정해서 `form`으로 입력받을 필드를 명시적으로 표시해야 합니다.  

`modelform_factory` 함수를 이용하는 방식은 이런 게 있구나~ 정도로 참고로 알아두시고, 우리는 `ModelForm` 클래스를 이용하는 방법으로 기존에 작성한 `form`을 바꿔볼게요.

### posts/forms.py  
```python
from django.forms import ModelForm
from .models import Post

class PostForm(ModelForm):
    class Meta:
        model = Post
        fields = [
            'title',
            'content',
            'image',
        ]
        labels = {
            'title': ('제목'),
            'content': ('내용'),
            'image': ('이미지'),
        }
        help_texts = {
            'title': ('제목을 입력해주세요.'),
            'content': ('내용을 입력해주세요.'),
        }


    def save(self, **kwargs):
        post = super().save(commit=False)
        post.user = kwargs.get('user', None)
        post.save()
        return post
```
`PostForm`을 한번 살펴볼게요.  
입력받을 `fields`로는 `title`, `content`, `image` 세 가지를 담았습니다. `user`는 유저로부터 입력받지 않고, `view`에서 채워넣을 것이기 때문에에 제외합니다.  

`labels`는 `form` 필드에 붙을 라벨의 내용입니다. 지정되지 않을 경우에 `Django`는 필드 이름에서 첫 번째 문자를 대문자로, 밑줄을 공백으로 변형하여 새로 생성해줍니다.  
예시: created_at :point_right: Created at

`help_texts`는 필드 사용법을 보여주는 추가적인 문구라고 생각하면 됩니다.  

이제 아래쪽에 `save` 함수에 대해서 알아볼게요.  
`ModelForm` 클래스에는 `save(self, commit=True)`라는 메서드가 내부적으로 구현되어 있습니다. 여기서 `commit`은 데이터베이스에 저장할지 여부를 결정하는 `flag`의 역할입니다. `commit=True`면 데이터베이스에 데이터를 바로 저장하고, `commit=False`면 저장을 지연시킵니다. 당장 저장하지 않을 때 사용하는 것이죠.  

우리는 이미 내부적으로 구현되어 있는 `save` 메서드를 오버라이드(override)할 겁니다.  
:bulb: 오버라이드는 객체 지향 프로그래밍에서 부모 클래스의 메서드를 똑같이 가져와 사용하는 것입니다. 자식 클래스에서 부모 클래스의 메서드를 재정의할 때 사용합니다.  
지금은 그냥 이미 있는 `save`를 이용하는 것이라고 생각하시면 되겠습니다.  

우선 `super()`를 이용해서 부모 메서드를 호출하는데, 데이터베이스에 데이터를 당장 저장하지 않겠다는 `commit=False`를 해 주었습니다. 자 여기서 왜 `commit=False`로 바꿔주었느냐 하면 우리는 `ModelForm`을 이용해서 제목, 내용, 이미지를 입력받았습니다. 그런데 새로운 글 작성을 할 때 필요한 게 하나 더 있죠. 바로 작성자입니다. 그래서 작성자에 대한 정보도 넣어준 다음에 저장을 해 줘야 합니다.  

`save` 함수의 `**kwargs`에 `user`라는 항목이 넘어올 건데요, 이걸 우리가 새로 생성할 `post`의 `user` 값으로 넣어줍니다. 그리고 저장을 하면 끝입니다.  

## View  
### posts/views.py  
```python
...
from .forms import PostForm

@login_required
def create(request):
    if request.method == "POST":
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            form.save(user = request.user)
            return redirect('home')
    else:
        form = PostForm()
        return render(request, 'posts/create.html', {'form': form})
```
자, 우선 상단에 우리가 새로 작성한 `PostForm`을 `import` 해줍시다.  
기존에는 `title`, `content`, `image`, `user` 값을 일일이 요청으로부터 읽어와서 변수에 넣어주고, 그걸 가지고 새로운 게시글을 만들어 주었잖아요? 이제 그럴 필요가 없어졌습니다.  

`POST` 방식으로 요청이 날아온다면, 먼저 `form`이라는 변수에 `PostForm` 객체를 만들고, `POST` 방식으로 온 데이터와 `FILES` 데이터를 넣어줍니다. (`image`는 `request.FILES.get('image')`로 가져왔던 것 기억하시죠?)  
`is_valid()` 함수는 `form`으로 입력받은 값들이 유효한지 판단해주는 함수입니다. 만약 유효하다면 `save` 함수를 호출하는데요, 이 함수는 `ModelForm`에 우리가 작성했던 그 `save` 메서드입니다.  
> `save` 함수의 `**kwargs`에 `user`라는 항목이 넘어올 건데요, 이걸 우리가 새로 생성할 `post`의 `user` 값으로 넣어줍니다.  

라고 말씀드린 것 기억하시나요? 요청을 보낸 사용자, `request.user`를 `user`라는 변수에 담아서 넘겨주는 겁니다.  
`ModelForm`의 `save` 메서드에서 글 작성자 정보도 넣고, 저장한 다음에 다시 `view`로 돌아와서 메인 페이지로 `redirect` 시켜줍니다.  

`POST`가 아닌 방식으로 요청이 날아온다면, `PostForm` 클래스의 객체를 만들어서 `form`이라는 변수에 넣어줍니다. 그리고 `html`을 띄울 때 딕셔너리 자료형으로 넣어서 전달해주도록 하겠습니다.  
왜 `form`이라는 변수를 전달하냐고요? `form`에 담긴 `PostForm`을 이용해서 게시글 작성 페이지를 간단하게 만들 수 있기 때문이에요!  

## Template  
### posts/templates/posts/create.html  
```html
<div class="container">
    <h2 class="mt-5">새로운 글 작성하기</h2>
    <form action="{% url 'posts:create' %}" method="POST" enctype="multipart/form-data">
        {% csrf_token %}
        {{ form.as_p }} # 각 필드가 paragraph 행으로 렌더링
        <input type="submit" value="작성하기" class="btn btn-primary">
    </form>
</div>
```
위와 같은 형태로 `view`에서 딕셔너리로 넘겨준 `form` 을 이용해 깔끔하게 글 작성 페이지를 보여줄 수 있게 되었습니다.  
`as_p`는 각각의 필드가 `paragraph`, 즉 `<p>`행으로 렌더링되도록 해 준 것이고, 만약 표 형태로 렌더링하고 싶다면
```html
{{ form.as_table }}
```
을 이용하시면 됩니다. 더 다양한 렌더링 옵션은 [공식 문서](https://docs.djangoproject.com/en/3.0/topics/forms/#form-rendering-options)를 참고해주세요.  
