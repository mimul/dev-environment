Homebrew 대해
---------------
Homebrew의 어원은 맥주를 스스로 양조해서 마신다는 뜻으로, 사용자가 스스로 패키지를 빌드하고 관리하게 해준다는 의미로 네이밍을 한 것 같다. 즉, Mac에서 사용자가 소프트웨어를 도입하는데 최소한의 비용을 들이고 이를 버전별로 관리해 주는 패키지 관리시스템 중에 하나라고 보면 된다. yum, apt-get 등과 비슷하다.

### 용어 정의
|   용어    |    원 의미            |     재해석                |
| :------- |:-------------------:|:------------------------ |
| Brew     | 양조                 | 소프트웨어 Build            |
| HomeBrew | 자가 양조             | 소프트웨어 자가 구축          |
| Celler   | 와인이나 맥주 등의 저장고 | 소프트웨어 설치 장소          |
| Keg      | 통                  | Build 자료               |
| Formula  | 단계별 요리법          | 빌드 방법/절차가 작성된 스크립트 |

Homebrew 설치
---------------
### 1. 사전 필요한 소프트웨어
- Java는 디폴트로 설치되어 있음.
- Command Line Tools은 App Store에서 [Xcode](https://itunes.apple.com/jp/app/xcode/id497799835?mt=12) 를 다운로드 할 수 있다. 설치가 완료되면 터미널에서 아래 커맨드를 실행한다.
	`$ xcode-select --install`
	Command Line Tools를 설치하면, gcc 같은 명령을 사용할 수 있게 된다.

### 2. Homebrew 설치
아래 커맨드를 실행하면 자동으로 설치된다.
```
$ ruby -e "$ (curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew doctor # 문제 진단을 해줌.
$ brew update # 최신버전 업데이트.
```

### 3. 기타
brew search 시에 만나게 되는 "GitHub API rate limit exceeded" 메세지 출현을 해결하기 이해 아래 방법대로 처리한다.
```
# 남은 조회 횟수 확인
$ curl -i https://api.github.com/users/whatever
# 그리고 https://github.com/settings/tokens에서 Generate new token버튼 클릭해서 토큰 생성.
$ vi ~/.zshrc
export HOMEBREW_GITHUB_API_TOKEN=f388c6xxxxxxxxxxxx
# 토큰 확인
$ curl  -i -H "Authorization: token f388c6xxxxxxxxxxxx" https://api.github.com/users/whatever
```

Homebrew 사용법
---------------
### 패키지 설치
```
$ brew install wget
```

### 패키지 비활성화 및 활성화
```
$ brew unlink wget # 일시적으로 비활성화
$ brew link wget   # 활성화
```

### 패키지 목록 업데이트
```
$ brew update      # formula를 업데이트
$ brew upgrade     # 업데이트 패키지를 다시 빌드
```

### 설치된 목록보기
```
$ brew list
```

### 패키지 제거
```
$ brew remove wget
```

### Homebrew 설정 목록
```
$ brew --config
HOMEBREW_VERSION: 0.9.5
ORIGIN: https://github.com/Homebrew/homebrew
HEAD: b2e5a220a7fb9e8ee14be122b2bd0d345dc3bdee
Last commit: 3 months ago
HOMEBREW_PREFIX: /usr/local
HOMEBREW_CELLAR: /usr/local/Cellar
CPU: 8-core 64-bit haswell
OS X: 10.10.4-x86_64
Xcode: N/A
CLT: 6.4.0.0.1.1435007323
Clang: 6.1 build 602
X11: N/A
System Ruby: 2.0.0-p481
Perl: /usr/bin/perl
Python: /Users/mimul/.pyenv/shims/python => /Users/mimul/.pyenv/versions/3.4.3/bin/python3.4
Ruby: /usr/bin/ruby
Java: 1.7.0_76
```

### Homebrew를 제거
```
$ cd `brew --prefix`
$ rm -rf Cellar
$ brew prune
$ rm `git ls-files`
$ rmdir Library/Homebrew Library/Aliases Library/Formula Library/Contributions
$ rm -rf .git
$ rm -rf ~/Library/Caches/Homebrew
```
