# include 사용해서 html 관리하기  
이전에 `extends`를 이용해서 html을 상속받는 과정을 알아보았는데요, 이번에는 `include`를 사용해서 html을 포함시키는 것을 알아볼게요.  
우리가 `base.html`에 navbar를 넣어두었는데, 만약에 navbar 코드가 더 길어지면 가독성도 떨어지고 유지보수하기 어려울 거에요. 그래서 이 navbar를 다른 html로 옮겨두고, 포함시키는 방식으로 불러오려고 합니다.  

저는 보통 다른 html에 포함되는 파일들은 프로젝트의 `templates/`디렉토리에 `shared/`라는 폴더를 하나 더 만들어서 관리하는 편입니다.
```
myblog/
├───manage.py
├───myblog/
        templates/
                base.html
                shared/
        __init__.py
        asgi.py
        settings.py
        urls.py
        wsgi.py
```
구조를 보면 위와 같은 형태입니다.  
저는 `shared/`에 들어가는 파일은 다른 html에 포함되는 것들이기 때문에 파일 이름 맨 앞에 `_`을 붙여서 구분을 해 둡니다. (안 붙이셔도 무방합니다.)  
`myblog/teplates/shared/`에 `_navbar.html` 파일을 만들어주고, `base.html`에 있던 `<nav></nav>`로 감싸져 있는 navbar 코드를 옮겨올게요.  

`_navbar.html`을 포함시킬 파일에 들어가서 알맞은 위치(여기서는 navbar가 들어갈 위치인 최상단)에
```python
{% include 'shared/_navbar.html' %}
```
이라고 작성해 주세요. 

:bulb: 만약 `extends`로 상속을 받은 상태에서 `include`로 다른 html 파일을 포함시키려면 `block` 내부에 `include` 부분이 들어가야 합니다.  

이처럼 `include`를 이용해서 템플릿에 템플릿을 포함시킬 수 있습니다.  
