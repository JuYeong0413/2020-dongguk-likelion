# 로컬에 Django 개발환경 설정하기  
## Windows  
#### 1. `Python` 최신버전 설치하기  
https://www.python.org/downloads/release/python-383/  
아래쪽에 Files 목록 중 본인 운영체제에 맞는 executable installer를 설치해주세요.  
- `32-bit` 운영체제: `x86`  
- `64-bit` 운영체제: `x86-64`  
`.exe` 파일 실행 후 아래쪽에 `Add Python 3.8 to PATH` 체크한 다음 설치를 진행해주세요.  
#### 2. `Python` 설치 확인  
`cmd`(명령 프롬프트)를 열어서 `Python`최신 버전이 설치가 잘 되었는지 확인합니다.  
```bash
$ python --version
```
#### 3. `vs code` 설치하기  
https://code.visualstudio.com/
`Download for Windows` 클릭해서 설치를 진행해주세요.  
설치 도중 `추가 작업 선택`에서 모두 체크한 다음 설치를 계속 진행해주세요.  
#### 4. [Git 설치하기](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/1st-semester/week-01/git.md#git-%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C)  
#### 5. `vs code` 환경설정  
- 바탕화면에서 마우스 오른쪽 버튼 클릭, `Git Bash Here` 클릭  
[Linux 명령어](https://github.com/JuYeong0413/2020-dongguk-likelion/blob/master/1st-semester/week-03/linux-commands.md)를 이용해 앞으로 장고 개발에 사용할 작업 디렉토리(폴더)를 만들어주고, 해당 디렉토리로 이동한 다음 `vs code`를 실행해주세요.  
```bash
$ mkdir [폴더이름]
$ cd [폴더이름]
$ code .
```
- `vs code`에서 terminal 창 띄우기  
상단 메뉴에서 `Terminal > New Terminal`(단축키 ``Ctrl + Shift + ` ``)  
terminal 창이 아래쪽에 생기는데 `cmd` 또는 `powershell`이 뜨는 경우, `View > Command Palette`(단축키 `Ctrl + Shift + P`)를 누른 다음 `select default shell`이라고 입력해주세요.  
아래쪽에 뜨는 것 중 `Git Bash`를 클릭해주세요.  
이후 기존 terminal 창을 지우고 새로 열거나, 새로운 terminal을 추가로 열면 `bash`로 뜹니다.  
#### 6. 가상환경 만들기  
```bash
$ pip3 install pipenv
```
#### 7. 가상환경 실행하기  
```bash
$ pipenv shell
```
#### 8. `Django` 설치하기  
```bash
$ pip install django
```
#### 9. 새로운 `Django` 프로젝트 생성하기  
```bash
$ django-admin startproject [프로젝트이름]
```
#### 10. `Django` 서버 실행하기  
:bulb: 반드시 `manage.py` 파일이 있는 프로젝트 폴더에서 서버를 실행해야 합니다.  
현재 위치에 `manage.py` 파일이 있는지 확인하는 방법? `ls` 명령어를 terminal에 입력해보세요.  
```bash
$ python manage.py runserver
```
Chrome을 열어서 `127.0.0.1:8000` 또는 `localhost:8000`으로 들어갔을 때 로켓이 보인다면 완료!  
#### 서버 종료하기  
`Ctrl + C` 키를 누르면 종료됩니다.  
