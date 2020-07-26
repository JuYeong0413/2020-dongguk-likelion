# 로컬에 Django 개발환경 설정하기  
## Mac  
#### 1. `Python` 최신버전 설치하기  
Mac에는 `Python`이 내장되어 있으나 버전이 2.x대이므로 최신 버전을 설치해주세요. [파이썬 다운로드](https://www.python.org/downloads/)  
#### 2. `Python` 설치 확인  
terminal을 열어서 `Python` 최신 버전이 설치가 잘 되었는지 확인합니다.  
```bash
$ python3 --version
```
#### 3. `Django` 프로젝트를 관리할 디렉토리 만들기  
terminal을 연 다음, [Linux 명령어](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/1st-semester/week-03/01-linux-commands.md)를 이용해 앞으로 장고 개발에 사용할 작업 디렉토리(폴더)를 만들어주고, 해당 디렉토리로 이동한 다음 `vs code`를 실행해주세요.  
```bash
$ mkdir [폴더이름]
$ cd [폴더이름]
$ code .
```
#### 4. 가상환경 만들기  
```bash
$ pip3 install pipenv
```
#### 5. 가상환경 실행하기  
```bash
$ pipenv shell
```
#### 6. `Django` 설치하기  
```bash
$ pip3 install Django
```
#### 7. 새로운 `Django` 프로젝트 생성하기  
```bash
$ django-admin startproject [프로젝트이름]
```
#### 8. `Django` 서버 실행하기  
:bulb: 반드시 `manage.py` 파일이 있는 프로젝트 폴더에서 서버를 실행해야 합니다.  
현재 위치에 `manage.py` 파일이 있는지 확인하는 방법? `ls` 명령어를 terminal에 입력해보세요.  
```bash
$ python manage.py runserver
```
Chrome을 열어서 `127.0.0.1:8000` 또는 `localhost:8000`으로 들어갔을 때 로켓이 보인다면 완료!  
#### 서버 종료하기  
`Ctrl + C` 키를 누르면 종료됩니다.  
  
## vs code  
#### 1. `vs code` 설치하기  
https://code.visualstudio.com/
#### 2. terminal에서 `vs code` 열기  
`vs code`를 실행시킬 디렉토리로 이동한 후  
```bash
$ code .
```
:bulb: 만약 에러가 표시되면 `vs code`에 직접 들어가서 `View > Command Palette`에서 `>shell command`를 입력한 다음 `Install 'code' command in PATH`를 클릭해주세요.  
