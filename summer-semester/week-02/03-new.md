# 글 작성 페이지 만들기  
지금까지 `posts` 앱에서는 `Post` 모델링만 진행했기 때문에, CRUD 기능을 구현하기 위한 준비를 시작해 볼게요.  

## Template  
먼저 글 작성을 할 수 있는 새로운 페이지가 필요하겠죠?  
글 작성을 위한 `form`을 띄워주는 `new.html` 파일을 `posts`의 `templates`로 생성해줍시다.  

### posts/templates/posts/new.html  
```html
{% extends 'base.html' %}

{% block content %}
<div class="container">
    <h1>새로운 글 작성하기</h1>
    <form action="#" method="POST">
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
</div>
{% endblock %}
```

<details>
  <summary><b>HTML 태그 설명</b></summary>
    
  #### form  
  블로그에 글을 작성한다고 생각을 했을 때, 사용자는 글의 제목과 내용을 입력해야 합니다.  
  입력한 데이터를 서버로 보내면 서버에서는 데이터를 저장한 다음 그 결과를 사용자에게 돌려주죠.  
  글 작성 뿐만 아니라 회원가입이나 로그인 등 사용자로부터 데이터를 받을 때 `form`이라는 양식을 사용하게 됩니다.  
  이런 양식을 사용하기 위해서는 `form` 태그가 필요한데요, 글을 작성할 때 제목과 내용을 입력하는 것도 하나의 `form`이고 로그인을 할 때 아이디와 비밀번호를 입력하는 것도 하나의 `form`이라고 볼 수 있습니다. 한 마디로 정보를 묶어서 보내는 태그라고 생각하시면 됩니다.  

  `form` 태그는 다양한 속성을 가지지만, 그 중에서 `action`과 `method`는 필수로 지정해야 하는 중요한 속성입니다.  
  - `action`: 데이터를 어디로 보낼 것인가? URL을 입력하는 부분  
  - `method`: 데이터를 어떤 방식으로 보낼 것인가? HTTP 메서드를 입력하는 부분(GET, POST, ...)  
  참고로 html에서 관리하는 기본 방식은 모두 `GET`입니다.  

  글을 작성할 때 전송되는 데이터의 크기가 크기 때문에, 우리는 `POST` 방식을 사용할 겁니다.  
  아직 글 작성을 한 다음 어디로 보낼 것인지 결정하지 않았으니, `action` 부분은 `#`으로 남겨주세요.  

  #### :bulb: `form`과 `link`의 차이  
  `form`은 입력값을 받아 어디론가 제출할 수 있고, `link`는 다른 곳(html 파일)으로 넘어가는 것입니다.

  #### input  
  `input` 태그는 입력 양식 태그 중 가장 자주 쓰이는 태그입니다. 주로 사용자에게 입력을 받을 때 사용되며, 빈 태그이기 때문에 닫는 태그를 사용하지 않습니다.  

  #### type  
  `input` 태그에는 `type`이라는 속성이 있는데, 어떤 값을 입력할 것인지 결정할 수 있습니다. 몇 가지만 알아봅시다.  
  - `text`: 한 줄의 입력 필드입니다.  
  - `password`: 비밀번호 입력을 위한 필드입니다.  
  비밀번호를 입력하는데 우리가 어떤 값을 입력했는지 화면에 표시되면 다른 사람이 볼 수도 있잖아요?  
  `password` 필드는 사용자가 입력한 값을 별표(`*`)나 동그라미(`•`)로 표시해줍니다.  
  - `checkbox`: 체크박스 필드입니다. 사용자는 2개 이상의 값을 선택할 수 있습니다.  
  - `radio`: 라디오 버튼 필드입니다. 라디오 버튼은 체크박스와 다르게 사용자가 하나의 값만 선택할 수 있습니다.  
  - `number`: 숫자 값을 입력받는 필드입니다. 최소값이나 최대값을 설정할 수 있습니다.  
  - `submit`: 사용자가 입력한 폼을 제출하는 버튼을 정의합니다.  
  - `reset`: 사용자가 입력한 폼의 모든 요소 값을 초기화시킵니다.  

  #### name  
  `input` 태그의 또 다른 속성으로 `name`이 있습니다. 이 속성도 매우 중요한데요, 사용자가 입력한 데이터를 구분하기 위해 `name` 속성을 키(key)로, `value` 속성을 값(value)으로 전송합니다.   
  
  #### placeholder  
  알아두면 좋을 속성을 하나 더 알려드릴게요. 바로 `placeholder` 입니다.  
  `placeholder`는 사용자에게 해당 필드에 대한 안내를 해 주는 역할을 한다고 생각하시면 돼요. 실제로 값이 있는 게 아닌, 힌트를 주는 텍스트입니다.  

  #### required  
  `required`는 필수로 입력해야 한다는 것을 나타내주는 속성입니다.  
  `Post` 모델링을 할 때, `title`을 `null=True`로 했던 것을 기억하시나요?  
  우리는 글 제목을 필수로 입력하게 했기 때문에 글 작성을 할 때 제목을 입력하지 않으면 폼 제출을 하지 못하도록 설정을 해 둘 겁니다.  

  ```html
  <input type="text" class="form-control" name="title" id="title" placeholder="제목을 입력해주세요." required>
  ```
  우리가 앞서 작성한 폼에서 `text` 타입의 `title`이라는 `name`을 가진 입력 태그를 추가한 걸 확인할 수 있죠.  
  사용자에게 제목을 입력해야 한다고 알려주는 `placeholder` 문구도 추가해 주었습니다.  
  마지막으로 `required`를 이용해서 `title` 필드가 빈 상태로 `submit`을 누르면 사용자에게 값을 입력하라고 알려주도록 했습니다.  

  #### textarea  
  `input` 태그의 `text` 타입은 한 줄을 입력할 수 있지만, 블로그 글 같은 경우에는 여러 줄을 입력받아야 하기 때문에 `input`이 아닌 `textarea` 태그를 사용해야 합니다.  
  `textarea` 태그는 `input`과 달리 여는 태그와 닫는 태그가 모두 필요합니다.  
  `rows`와 `cols` 속성으로 창의 크기를 직접 지정할 수도 있습니다.  

  ```html
  <textarea class="form-control" name="content" id="content" placeholder="내용을 입력해 주세요..."></textarea>
  ```
  마찬가지로 앞에서 작성한 폼의 `textarea` 부분을 가져와봤어요.  
  `content`라는 `name`을 가지게 했고, `placeholder`로 내용을 입력하는 부분이라는 걸 사용자에게 안내해주도록 했습니다.  

  #### label  
  `input`태그와 세트로 자주 사용되는 `label` 태그에 대해서 알아볼게요.  
  `label`은 `input`의 이름표 역할이라고 생각하시면 됩니다.  
  `for` 속성을 이용해서 어떤 `input`을 알려주는지 설정할 수 있죠. `for` 속성의 값으로는 `input` 태그의 `id`값이 들어가야 합니다.  
  ```html
  <label for="title">글 제목</label>
  ...
  <label for="content">글 내용</label>
  ```
  우리는 제목과 내용에 해당하는 `label`을 각각 만들어 주었는데요, `for` 속성의 값으로 각각 `title`, `content`를 넣어주었습니다.  
  나중에 서버를 실행시킨 다음, 글 작성 페이지에서 `label` 부분을 클릭하면 해당 `label`에 연결된 입력 양식에 자동으로 포커스가 생기는 것을 확인할 수 있습니다.  
</details>

`Template` 작성이 끝났으니, `View`로 넘어가봅시다.  

## View  
### posts/views.py  
글 작성 페이지를 화면에 띄워주는 함수를 하나 작성해줄게요.  
```python
from django.shortcuts import render

def new(request):
    return render(request, 'posts/new.html')
```

## URL  
### posts/urls.py  
`posts` 앱과 관련된 URL을 따로 관리해주기 위해 `urls.py` 파일을 새로 만들고, `path`를 작성해줄게요.  
```python
from django.urls import path
from .views import new

app_name="posts"
urlpatterns = [
    path('new/', new, name="new"),
]
```
여기서 한 가지 주목할 점은 바로 두 번째 줄에서 `views`를 `import`해오는 부분입니다.  
기존에 작성했던 `introduction` 앱의 `views.py` 파일을 한번 확인해볼게요.  

### introduction/urls.py  
```python
from django.urls import path
from . import views

app_name='introduction'
urlpatterns = [
    path('', views.profile, name="profile"),
]
```
차이점이 보이시나요?  

`introduction`에서는 `views` 파일 자체를 `import` 해왔는데, `posts`에서는 `views`에 정의된 개별 함수(여기서는 `new`)를 `import`하고 있습니다.  
그렇기 때문에 `introduction`에서는 `path`에 `views.함수이름`의 형태로 작성해 주었지만, `posts`에서는 `path`에 `함수이름`만 작성해줘도 됩니다.  


### myblog/urls.py  
```python
urlpatterns = [
    ...
    path('posts/', include('posts.urls')),
]
```
`posts`의 `urls.py` 작성이 끝났으니, `myblog`의 `urls.py`에서 `include`를 이용해 `posts` 앱의 url을 관리하게 설정해줍시다.  

### myblog/templates/shared/_navbar.html  
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <a class="navbar-brand" href="{% url 'main' %}">My Blog</a>
  
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav mr-auto">
        <li class="nav-item active">
          <a class="nav-link" href="{% url 'main' %}">Home <span class="sr-only">(current)</span></a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{% url 'introduction:profile' %}">Profile</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{% url 'posts:new' %}">New</a>
        </li>
    </div>
</nav>
```
우리의 navbar에서 글 작성 페이지로 바로 이동할 수 있도록 링크를 걸어줍시다.  

이제 서버를 실행시켜서 navbar의 New를 클릭해 보세요. 글 작성 페이지로 잘 넘어가는 걸 확인할 수 있습니다.  
