Mac에 필요한 앱 설치
======================
Mac에서 개발자가 자주 사용하는 앱들에 대해서 설치, 설정 정보들을 공유한다.

Mac 기본 앱 자동 설치
---------------
[getmacapps][getmacapps]에서 설치할 기본앱들을 설정하고 자동 설치 URL 받아서 맥에서 자동 설치한다. 필요한 앱은 `Chrome, Firefox, Dropbox(Spec 등의 문서 저장 및 공유 도구로 활용), Evernote(개발 등의 메모장으로 활용), FileZilla, Sublime Text 2, iterm2, Alfred` 들을 선택하고 `install these!`를 클릭하면 아래 커맨드를 알려준다. 이 커맨드를 맥에서 실행하면 자동으로 선택된 앱들이 설치된다.
```
$ curl -s http://www.getmacapps.com/raw/d | sh
```

추가 앱 설치
---------------
- 개발도구 : [Intellij](https://www.jetbrains.com/idea/)
- DB 관리 도구 : [Navicat](http://www.navicat.com/)
- Markdown Editor : [HarooPad](http://pad.haroopress.com/user.html)
- 업무용 채팅 : [Slack](https://slack.com/apps)
- 기타 채팅 : [Telegram](https://www.telegram.org/)
- 압축 유틸리티 : [The Unarchiver](https://itunes.apple.com/en/app/the-unarchiver/id425424353?mt=12)

Sublime Text 2 단축키 정보
---------------
- 설정
	```
    # Sublime Text -> Preferences -> Settings - User에 아래 설정 추가.
    "ensure_newline_at_eof_on_save": true,
    "show_full_path": true,
    "tab_size": 2,
    "translate_tabs_to_spaces": true,
    "trim_trailing_white_space_on_save": true
    ```

- 단축키
    ```
    - control + shift + 방향키 : 열모드 블럭 지정
    - control + shift + K : 현재 커서 라인 삭제
    - command + U : undo
    - command + shift + U : redo
    - command + F : 해당 파일에서 내용 찾기
    - shift + command + F : 전체 파일에서 내용 찾기
    - option + command + F : 해당 파일에서 문자열 교체
    - shift + control + 아래화살표 : 칼럼단위 블럭 지정
    - option + command + F : 해당 파일의 검색 문자열을 교체
    - command + X : 현재라인 삭제
    - command + ] : 들여쓰기
    - command + [ : 내어쓰기
    - command + 1,2,3,4 : 탭이동
    - alt + command + 1,2,3,4 : 개수만큼 수직 분할
    - shift + control + 1,2,3,4 : 현재 탭을 수직 분할된 창으로 이동
    - control + 1,2,3,4 : 수직 분할된 창 선택
    - shift + command + P : package install 창
    - command + W : 현재탭 닫음
    - shift + command + T : 닫은탭 재오픈
    ```

intellij 설치 및 설정
---------------
- Tomcat 설치
```
$ brew install tomcat
# 설치 위치 : /usr/local/Cellar/tomcat/8.0.24
```
Edit Configuration에서 Add New Configuration클릭한 다음 Tomcat Server>Local을 선택한다. 그리고 Server탭에 Vm option주고, Deployment탭에서 `+`호 눌러서 Artifact를 추가한다.

- 테마 변경
  Preferences>Appearance>Theme를 Darcula로

- 행의 끝 이후로 커서가 이동하지 않도록
 Preferences>Editor>General>Allow placement of caret after end of line 체크 해제.

- 행번호 표시
 Preferences>Editor>General>Appearance>Show line numbers 체크.

- 편집기에서 표시되는 파일 및 프로젝트 트리의 선택 파일을 동기화
 해당 프로젝트>설정> Autoscroll from Source 선택

- 키맵 설정
 Preferences>Keymap

- 자동 저장
 General>Syncronization

- Boilerplate Code 회피
 Plugins>Browse Repositories>Lombok

- Eclipse Code Formatter
 Plugins>Eclipse Code Formatter 검색해서 설치
 Other Settings>Eclipse Code Formatter>Use the eclipse code formatter enable
 Editor>Code Style>Java>Scheme Manage>Import> Google formater 선택(eclipse-java-google-style.xml)

- 기타
 Editor>Inspections>Serialization Issues의 Serializable class without 'serialVersionUID'에 체크.

- Info.plist
    ```
    $ vi /Applications/IntelliJ IDEA 14.app/Contents/Info.plist
    <key>JVMVersion</key>
    <string>1.7*</string>
    ```

- idea.vmoptions 설정
    ```
    > vi /Applications/IntelliJ IDEA 14.app/Contents/bin/idea.vmoptions
    -Xms2048m
    -Xmx2048m
    -XX:MaxPermSize=350m
    -XX:ReservedCodeCacheSize=225m
    -XX:+UseCompressedOops
    -Dsun.io.useCanonCaches=false
    -Djava.net.preferIPv4Stack=true
    -Djsse.enableSNIExtension=false
    -Dawt.useSystemAAFontSettings=lcd
    ```

- Tomcat VM Option
```
-Dfile.encoding=UTF-8 -Xms1024m -Xmx1024m -XX:PermSize=64m -XX:MaxPermSize=256m
```

- 단축키 정보
    ```
    - shift + command + 방향키 : 현재 커서에서 라인끝까지 블럭
    - command + D : 현재 커서의 라인을 복사해서 다음라인에 paste
    - command + O : 원하는 클래스 검색
    - shift + command + O : 원하는 파일 검색
    - command + L : 원하는 라인으로 이동
    - command + F : 현재 파일에서 검색
    - shfit + command + F : 프로젝트 전체 파일내 검색
    - option + command + L : code formatting
    - command + R : 해당 파일에서 문자열 교체
    - shift + command + R : 프로젝트내의 전체 파일의 해당 문자열 교체
    - command + ; : 해당 프로젝트 설정 창으로 이동
    - command + , : Preferences창 이동
    - command + / : 해당 라인 주석처리, 블럭 설정시 해당 블럭 주석, 한번더 실행하면 주석 해제
    - option + command + T : for, if 등 문구 자동 추가
    - control + shift + R : 해당 클래스 실행
    - control + tab : 탭간의 이동
    - command + W : 현재탭 닫기
    - control + option + O : 불필요한 import 정리
    - option + Enter : auto import
    - alt + command + 1 : 수직 분할
    - Goto Next Splitter : alt + tab
    - Goto Next Splitter : alt+ shift + tab
    ```

[getmacapps]: http://www.getmacapps.com
