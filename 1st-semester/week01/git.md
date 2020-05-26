# Git  
## Git이란?  
- 개발과정, 소스파일 등을 관리하는 도구  
- `version management system`의 3가지 특징: `Backup`, `Recovery`, `Collaboration`
  - 개발한 과정(역사)을 저장하고 확인할 수 있음  
  - 특정시점으로 복구 가능  
  - 다른 사람과 협업 가능  

## Git 기본설정  
`local`에서 처음 `git`을 사용할 경우, 사용자 등록을 해 주어야 한다.  
1. `Git Bash` 혹은 `terminal` 실행  
2. 본인의 `Github 계정 이메일`과 본인 `영문이름`(Github 아이디 X)을 적기  
큰따옴표도 적어야 한다!  
```bash
$ git config --global user.email "[Github 계정 이메일]"
$ git config --global user.name "[본인 영문이름]"
```

## Git 필수 명령어  
### init
해당 디렉토리(폴더)에 git 초기화(initializing)  
버전 관리를 시작하겠다는 의미로, 한 번만 해 주면 됨  
```bash
$ git init
```

### Github 원격저장소 연결
```bash
$ git remote add origin [Github repository URL]
```

### status  
Git 상태확인  
```bash
$ git status
```
`untracked files`: 빨간색으로 표시됨, 아직 `stage`에 올리지 않은 파일들  
`tracked files`: 초록색으로 표시됨, `stage`에 올라간 파일들  

### log
지금까지 만든 버전(`commit`)의 목록 확인  
```bash
$ git log
```
아래에서 위로 올라갈수록 최신순이며, `q`키를 눌러 종료할 수 있음  

### add  
작업된 사항을 `stage`(`commit`할 목록)에 추가  
하나의 파일만 추가하려면  
```bash
$ git add [파일명]
```
현재 경로에 있는 `모든 파일`을 `commit`할 목록에 추가하려면  
```bash
$ git add .
```
또는  
```bash
$ git add -A
```

### commit  
`stage`에 올라와 있는 것들을 버전(history)의 한 단위로 만들기  
```bash
$ git commit -m "[commit 메시지]"
```

### commit 메시지 수정
가장 최근 `commit` 메시지 수정하기  
```bash
$ git commit --amend -m "[수정할 메시지]"
```
만약 `Github repository`(원격저장소) `master` branch에 `push`한 이후에 `commit` 메시지를 수정했다면 원격저장소에도 이를 반영해줘야 한다.  
강제로 `push`해서 수정하기  
```bash
$ git push --force origin master
```
또는  
```bash
$ git push -f origin master
```

### push  
현재까지의 `commit`(history)를 `Github repository`(원격저장소)의 `master` branch에 밀어넣기  
```bash
$ git push origin master
```

### reset
가장 최근 `commit` 지우기  
```bash
$ git reset HEAD~1
```
