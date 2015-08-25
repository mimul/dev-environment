Mac 개발 환경 구축 가이드
======================

Homebrew 설치
---------------
- Java, Command Line Tools은 기본적으로 설치되었다고 가정한다.
```
$ ruby -e "$ (curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew doctor # 문제 진단을 해줌.
$ brew update # 최신버전 업데이트.
```
- 참조 문서 : [Homebrew에 설치 및 사용법(상세)][homebrew]

Git 설치
---------------
- 기본 설치
```
$ brew install git
```

- 참조 문서 : [Git 설치, 설정, 사용법(상세)][mac_git]

dotfiles로 iTerm2 + zsh + tmux + vim + Git 설치/설정 자동화하기
---------------
zsh와 tmux, vim 설치 및 설정을 자동화해서 편하게 맥에서 환경을 구축할 수 있게 해준다. 그리고 수정, 개선사항은 github의 [dotfiles][git_dotfiles] 리파지토리에 관리하면 로컬 맥에서 .dotfiles에서 `make update` 명령어만 날리면 로컬 환경에 자동 반영된다.
```
$ git clone https://github.com/mimul/dotfiles.git ~/.dotfiles
$ cd ~/.dotfiles && make install
$ vim +NeoBundleInit +qall # Vim 플러그인 설치
$ chsh -s /bin/zsh # zsh 설정
```

위 커맨드를 실행하면 아래와 같은 화면을 볼 수 있다.

![사진 1][mac_terminal]

### 1. 커맨드 관련 편리한 기능들
```
cd - Enter : 과거 커맨드 중복 제거된 히스토리 보여줌
cd Enter   : 현재창에서 과거에 입력한 커맨드 보여줌.
```

### 2. Tmux 단축키 정보
- *기본 구성*
	* 상태바에는 접속 wifi, 배터리, 날짜 정보가 보임.
- *커맨드*
```
$ tmux ls : 기존 세션 확인.
$ tmux -a : 기존 세션 붙기
$ tmux list-window : tmux로 열린 세션 보기
```

- *화면 창 조작*
```
$ control + t + h : active pane 이동(오른쪽).
$ control + t + l : active pane 이동(왼).
$ control + t + | : pane 수직 분할.
$ control + t + - : pane 수평 분할.
$ control + t + o : pane 순서대로 로테이트.
$ control + t + q : pane 죽임.
```
- *Copy Mode*
	* 복사/붙여넣기
	control + t + v 하면 Copy Mode로 바뀐다. 이것으로 버퍼에 문자열을 카피할 수도 있고,
	Space 후 방향키 등으로 범위를 정해 Enter키를 눌러 복사하고, control + t + p 하면 붙여넣기가 된다.
	* 화면 이동하기
	일반적으로 터미널 화면을 넘어간 텍스트를 보고 싶을 때는 "스크롤바" 를 움직이면 되었는데, tmux에서는 Copy mode 상태에서 이동이 가능하다.
	이때 위로 스크롤 넘어갈 경우 방향키나 vi 이동키를 활용할려면, `set-window-option -g mode-keys vi` 를 통해 vi 이동키인 h, j, k, l 로 이동할 수 있다.

### 3. zshrc 설정 정보
```
$ .. : "cd .."와 동일. 이전 데릭토리로 갈 리스트 보여줌.
$ cd - Enter : 과거 커맨드 중복 제거된 히스토리 보여줌
$ cd Enter : 과거에 입력한 커맨드 보여줌.

# alias -g C='| pbcopy'
$ cat ~/.ssh/id_rsa.pub C : "cat ~/.ssh/id_rsa.pub | pbcopy"와 동일한 명령어로 id_rsa.pub의 내용을 클립보드에 복사.
```

### 4. Vim 설정 정보
```
- 검색 하이라이트 지우기 : ESC 두번 클릭
- 중괄호({}), <>, () 대응.
- 문자 코드 자동 인식
- 개행 코드 자동 인식
- (중)괄호 자동 완성
```

- 단축키 정보
![사진 2][vim_image]

-  참조 문서 : [vim 점진적으로 학습하기][vim]


필요한 앱 설치
---------------

[homebrew]: https://github.com/mimul/dev-environment/blob/master/mac-homebrew.md
[mac_git]: https://github.com/mimul/dev-environment/blob/master/mac-homebrew.mddotfiles로 iTerm2 + zsh + tmux + vim + Git 설치/설정 자동화하기
---------------
zsh와 tmux, vim 설치 및 설정을 자동화해서 편하게 맥에서 환경을 구축할 수 있게 해준다. 그리고 수정, 개선사항은 github의 [dotfiles][git_dotfiles] 리파지토리에 관리하면 로컬 맥에서 .dotfiles에서 `make update` 명령어만 날리면 로컬 환경에 자동 반영된다.
```
$ git clone https://github.com/mimul/dotfiles.git ~/.dotfiles
$ cd ~/.dotfiles && make install
$ vim +NeoBundleInit +qall # Vim 플러그인 설치
$ chsh -s /bin/zsh # zsh 설정
```

위 커맨드를 실행하면 아래와 같은 화면을 볼 수 있다.

![사진 1][mac_terminal]

### 1. 커맨드 관련 편리한 기능들
```
cd - Enter : 과거 커맨드 중복 제거된 히스토리 보여줌
cd Enter   : 현재창에서 과거에 입력한 커맨드 보여줌.
```

### 2. Tmux 단축키 정보
- *기본 구성*
	* 상태바에는 접속 wifi, 배터리, 날짜 정보가 보임.
- *커맨드*
```
$ tmux ls : 기존 세션 확인.
$ tmux -a : 기존 세션 붙기
$ tmux list-window : tmux로 열린 세션 보기
```

- *화면 창 조작*
```
$ control + t + h : active pane 이동(오른쪽).
$ control + t + l : active pane 이동(왼).
$ control + t + | : pane 수직 분할.
$ control + t + - : pane 수평 분할.
$ control + t + o : pane 순서대로 로테이트.
$ control + t + q : pane 죽임.
```
- *Copy Mode*
	* 복사/붙여넣기
	control + t + v 하면 Copy Mode로 바뀐다. 이것으로 버퍼에 문자열을 카피할 수도 있고,
	Space 후 방향키 등으로 범위를 정해 Enter키를 눌러 복사하고, control + t + p 하면 붙여넣기가 된다.
	* 화면 이동하기
	일반적으로 터미널 화면을 넘어간 텍스트를 보고 싶을 때는 "스크롤바" 를 움직이면 되었는데, tmux에서는 Copy mode 상태에서 이동이 가능하다.
	이때 위로 스크롤 넘어갈 경우 방향키나 vi 이동키를 활용할려면, `set-window-option -g mode-keys vi` 를 통해 vi 이동키인 h, j, k, l 로 이동할 수 있다.

### 3. zshrc 설정 정보
```
$ .. : "cd .."와 동일. 이전 데릭토리로 갈 리스트 보여줌.
$ cd - Enter : 과거 커맨드 중복 제거된 히스토리 보여줌
$ cd Enter : 과거에 입력한 커맨드 보여줌.

# alias -g C='| pbcopy'
$ cat ~/.ssh/id_rsa.pub C : "cat ~/.ssh/id_rsa.pub | pbcopy"와 동일한 명령어로 id_rsa.pub의 내용을 클립보드에 복사.
```

### 4. Vim 설정 정보
```
- 검색 하이라이트 지우기 : ESC 두번 클릭
- 중괄호({}), <>, () 대응.
- 문자 코드 자동 인식
- 개행 코드 자동 인식
- (중)괄호 자동 완성
```

- 단축키 정보
![사진 2][vim_image]

-  참조 문서 : [vim 점진적으로 학습하기][vim]


필요한 앱 설치
---------------
맵에서 필요한 앱 설치는 [이곳][mac_apps]을 참고하여 설치 및 셋팅을 진행하면 된다.

[homebrew]: https://github.com/mimul/dev-environment/blob/master/mac-homebrew.md
[mac_git]: https://github.com/mimul/dev-environment/blob/master/mac-git.md
[mac_terminal]: http://i.imgur.com/sDxus3j.png
[git_dotfiles]: https://github.com/mimul/dotfiles
[vim]: http://www.mimul.com/pebble/default/2014/07/15/1405420918073.html
[vim_image]: http://www.mimul.com/pebble/default/images/blog/tech/vi-vim-cheat-sheet-ko.png
[mac_terminal]: http://i.imgur.com/sDxus3j.png
[git_dotfiles]: https://github.com/mimul/dotfiles
[vim]: http://www.mimul.com/pebble/default/2014/07/15/1405420918073.html
[vim_image]: http://www.mimul.com/pebble/default/images/blog/tech/vi-vim-cheat-sheet-ko.png
[mac_apps]: https://github.com/mimul/dev-environment/blob/master/mac-apps.md