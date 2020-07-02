# html 확장하기  
navigation bar, footer 등 여러 페이지에 포함되는 부분을 html 파일에 각각 작성하면 코드 중복, 유지보수가 어렵습니다.  
뼈대를 만들어서 다른 html 파일에서 재활용할 수 있는 방법을 알아볼게요.  

### settings.py  
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```
이 부분이 `Django` 프로젝트의 `templates` 설정에 대한 부분입니다.  
`DIRS` 부분의 대괄호(`[]`) 안에 아래 항목을 추가해줍시다.  
```python
os.path.join(BASE_DIR, 'myblog', 'templates')
```
우리가 앱의 html을 `templates`로 관리한 것처럼, 뼈대가 될 html 파일은 프로젝트에서 관리를 할 거에요.  
그러면 프로젝트 폴더에도 html 파일들을 담아 둘 `templates` 폴더가 필요하겠죠?
`myblog` 폴더 안의 `templates`라는 경로를 읽어올 수 있게 설정하는 것입니다.  

경로 설정을 완료했으니, `myblog/` 폴더 안에 `templates/` 폴더를 새로 생성해 줍시다.  
그리고 그 안에 `base.html`이라는 파일도 만들어 줄게요.  
### base.html
`base.html`에는 먼저 [`Bootstrap`의 starter template](https://getbootstrap.com/docs/4.5/getting-started/introduction/#starter-template)을 가져와서 넣어줄게요.  
```html
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">

    <title>Hello, world!</title>
  </head>
  <body>
    <h1>Hello, world!</h1>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/js/bootstrap.min.js" integrity="sha384-OgVRvuATP1z7JjHLkuOU7Xw704+h835Lr+6QL9UvYjZE3Ipu6Tp75j7Bh/kR0JKI" crossorigin="anonymous"></script>
  </body>
</html>
```
`Bootstrap`을 사용하기 위해서 필요한 cdn도 이미 포함되어 있네요.  
여기서 잠깐 html의 구조를 짧게 다시 짚어보고 넘어갈게요.  
`<head></head>` 부분은 `meta` 태그나 `css` 등 직접적으로 보이지 않지만 `<body></body>`에 있는 내용을 꾸며주는 부분입니다. 그 중에서 `<title></title>`은 브라우를 실행했을 때의 탭 이름이에요.  
```html
<title>Hello, world!</title>
```
부분을
```html
<title>My Blog</title>
```
로 바꿔줄게요.  
그리고 `<body></body>` 부분에 [`navbar`](https://getbootstrap.com/docs/4.5/components/navbar/#supported-content)도 추가해줍니다. `navbar`는 좌측에  Components 탭 안에  있습니다.  
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Navbar</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>

  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Link</a>
      </li>
      <li class="nav-item dropdown">
        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
          Dropdown
        </a>
        <div class="dropdown-menu" aria-labelledby="navbarDropdown">
          <a class="dropdown-item" href="#">Action</a>
          <a class="dropdown-item" href="#">Another action</a>
          <div class="dropdown-divider"></div>
          <a class="dropdown-item" href="#">Something else here</a>
        </div>
      </li>
      <li class="nav-item">
        <a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">Disabled</a>
      </li>
    </ul>
    <form class="form-inline my-2 my-lg-0">
      <input class="form-control mr-sm-2" type="search" placeholder="Search" aria-label="Search">
      <button class="btn btn-outline-success my-2 my-sm-0" type="submit">Search</button>
    </form>
  </div>
</nav>
```
:bulb: `<body>` 시작 태그 바로 아래쪽, JavaScript 위에 들어가게 추가해주세요.  
자 이렇게 우리 블로그 프로젝트의 뼈대가 완성되었습니다.  

이 뼈대를 재사용하려면, 뼈대 안에 들어갈 내용의 위치를 지정해 주어야 합니다. 바로 `block`을 만들어 주는 겁니다.  
navbar는 보통 페이지 최상단에 위치하니까 우리는 `block`을 `<nav></nav>` 태그 아래에 넣어줍시다.  
```python
{% block content %}
{% endblock %}
```
뼈대에 들어갈 내용의 위치가 정해졌으니, 이제 내용이 포함되어 있는 html에도 뼈대를 사용할 거라는 것을 알려주어야 합니다.  
```python
{% extends 'base.html' %}
```
위와 같은 방식으로 뼈대를 가져오는 겁니다.  
html 파일의 맨 위에 들어가야 합니다.  
`extends`를 이용해 html 파일을 가져올 수 있는 이유는 우리가 `settings.py`에 `templates` 경로를 잡아주었기 때문입니다.  

`base.html`에 우리가 내용의 위치를 `block`으로 정해주었으니, 내용이 포함된 html도 `block`으로 감싸주어야 합니다.  
```python
{% extends 'base.html %}

{% block content %}
...
{% endblock %}
```
이런 형태가 되는 것이죠.  
서버를 실행시켜 보면 navbar가 잘 적용되어 있는 것을 확인할 수 있습니다.  
참고로, `extends`로 상속받은 경우 `block` 외부에 있는 코드는 무의미해집니다.  
