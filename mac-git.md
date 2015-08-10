Mac 개발환경 구축 - git
======================
(주)Wiseeco에서 Mac으로 개발에 들어가기전 필요한 git 개발을 위한 최소한의 설정을 기술하며, 아래 기술한 내용들을 무조건 따라야하는 것은 아니고 처음 git을 접했을때 헤매지 않도록 하기 위한 최소한의 장치라고 생각하자.

git 설치
---
```
> brew install git
> ssh-keygen
> git config --global user.name "mimul"
> git config --global user.email "hahojin@gmail.com"
> git config --global color.ui auto
```
git 기본 환경 설정 - config
---
git을 사용하는데 필요한 설정 정보를 기술한다.

#### 1. 환경 설정
```
> vi ~/.gitconfig
[user]
        name = mimul
        email = hahojin@gmail.com
[color]
        ui = auto
[credential]
    helper = cache --timeout 3600
[alias]
    lg = log --graph --date=short --decorate=short --pretty=format:'%C(white)%h%Creset %C(blue)%cn%Creset %C(green)%cd%Creset %C(red reverse)%d%Creset %C(reset)%s' --all
    dlg = log --stat --decorate=full --pretty=format:'%C(yellow)%h %C(green)%cr %C(blue)%cn%Creset %C(red reverse)%d%Creset %Creset%s %C(cyan)%b'
    mlg = log --merges --pretty=format:'%C(yellow)%h %Cgreen%ci %Cblue%cn%Creset %C(red reverse)%d%Creset %Creset%s %C(cyan)%b'
    sync = !git checkout master && git pull origin master && git fetch -p origin && git branch -d $(git branch --merged | grep -v master | grep -v '*')
```
#### 2. alias 설명

 - git lg는 과거 로그 이력을 보여줌.
 ![사진 3][git_lg]
 - git dlg는 과거 좀 더 디테일한 로그를 보여주어 변경 사항을 확인해서 리뷰에 도움을 줌.
 ![사진 1][git_dlg]
 - git mlg는 머지된 로그만 보여줌.
 ![사진 2][git_mlg]
 - git sync는 현재 master를 최신 상태로 한 후 branch를 삭제함. branch를 삭제하기에 유의해야 함.

#### 3. 커밋 로그 포멧 설명
3.1 형식 설명
```
%h : 해시(약어), Hash의 약자
%H : 해시
%s : 커밋 메시지 제목, Subject의 약자
%d : 참조 이름(브랜치, 태그, 원격), Decorate의 약자
%an : Author 이름
%ae : Author 이메일 주소
%ad : Author 날짜
%ar : Author 상대날짜
%cn : Committer 이름
%ce : Committer의 이메일 주소
%cd : Committer 날짜
%cr : Committer 상대 날짜

%C는 색상 변경
 . 색상 : normal, black, red, green, yellow, blue, magenta, cyan, white)
 . 특성 : bold (굵게), dim (감광) ul (밑줄), blink (점멸), reverse (반전)

%Creset : 색상 속성의 형식을 해제
```

3.2 날짜 변경
```
--date:iso : 상대날짜
--date:relative : 상대 시간(예- 1 days ago)
--date:local : 현지 시간대
--date:iso : ISO 8601 포맷
--date:rfc : RFC 2822 형식
--date:short : YYYY-MM-DD
--date:raw : %s %z
--date:default : 표준
```
#### 4. gitignore 설정
git 관리에서 제외할 대상들을 기술한다.
```
> vi ~/.gitignore
# Mac OS X hidden files
.DS_Store
.settings
.classpath
.project
.idea
*.iml
bin/
classes/
lib/
target/
node_modules/
src/main/resources/rebel.xml
> git config --global core.excludesfile ~/.gitignore
```

[git_dlg]: http://www.mimul.com/pebble/default/images/blog/Projects/git_dlg.png
[git_mlg]: http://www.mimul.com/pebble/default/images/blog/Projects/git_mlg.png
[git_lg]: http://www.mimul.com/pebble/default/images/blog/Projects/git_lg.png