# Django 프로젝트 구조  
```bash
$ django-admin startproject firstproject
$ cd firstproject/
```
로 `firstproject`라는 이름의 새로운 `Django` 프로젝트를 생성한 다음, 프로젝트 디렉토리로 이동했다고 가정해 봅시다.  
그러면 아래와 같은 폴더/파일 구조를 볼 수 있습니다.  
```
firstproject/
├───manage.py
├───firstproject/
        __init__.py
        asgi.py
        settings.py
        urls.py
        wsgi.py
```
## manage.py
앱(app)을 생성하고, 데이터베이스(database)에 대한 작업을 하고, 서버를 시작하는 일 등에 사용됩니다.  
서버를 시작하는 명령어가 `python manage.py runserver`였던 것 기억하시죠?  
`Django` 프로젝트와 다양한 방법으로 상호작용하는 커맨드라인(command-line) 유틸리티라고 생각하시면 됩니다.  

## firstproject/ 디렉토리
`firstproject` 디렉토리 내부에는 `Django` 프로젝트를 위한 실제 `Python` 패키지들이 저장됩니다.  

## __init__.py  
`__init__.py` 파일은 빈 파일입니다. 이 파일은 `Python`으로 하여금 해당 디렉토리(여기에서는 `firstproject/`)를 패키지처럼 다루라고 알려주는 용도입니다.  

## asgi.py  
`ASGI`는 `Asynchronous Server Gateway Interface`의 약자로, 웹 서버와 프레임워크, 애플리케이션을 비동기로 연결해 주는 `Python`의 표준 인터페이스라고 보시면 됩니다. 지금은 이런 게 있다 정도로만 알아두셔도 괜찮습니다.  

## settings.py  
현재 `Django` 프로젝트의 모든 환경 및 구성에 대한 설정이 있는 파일입니다. 우리가 만드는 앱도 등록이 되고, static files의 위치, database 세부 설정 등이 포함됩니다. 

## urls.py
사용자가 접근하는 웹사이트의 `url`과 `view`의 연결을 지정해줍니다. 이 파일에는 모든 `url` 매핑 코드가 포함될 수 있지만, 일반적으로 앱 단위로 `url`을 관리하는 것을 권장합니다.  

## wsgi.py  
`WSGI`는 `Web Server Gatewy Interface`의 약자로, `Python`의 표준 Gateway Interface 입니다. 현재 `Django` 프로젝트를 서비스하기 위한 `WSGI` 호환 웹 서버의 진입점이라고 하는데요, 이것 또한 지금은 깊게 알아둘 필요는 없습니다. 사이트 배포단계에서 자세하게 알려드릴게요.  

