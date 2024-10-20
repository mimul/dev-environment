Mac 개발환경 구축 - git
======================
Mac으로 개발에 들어가기전 필요한 git 개발을 위한 최소한의 설정을 기술하며, 아래 기술한 내용들을 무조건 따라야하는 것은 아니고 처음 git을 접했을때 헤매지 않도록 하기 위한 최소한의 장치라고 생각하자.

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
    ls = !git lg | fzf -m --ansi --layout=reverse --preview=\"echo {} | sed 's/-.*$//' | egrep -o '[0-9a-f]+' | xargs git show --color=always\" | sed 's/-.*$//' | egrep -o '[0-9a-f]+'
    dlg = log --stat --decorate=full --pretty=format:'%C(yellow)%h %C(green)%cr %C(blue)%cn%Creset %C(red reverse)%d%Creset %Creset%s %C(cyan)%b'
    mlg = log --merges --pretty=format:'%C(yellow)%h %Cgreen%ci %Cblue%cn%Creset %C(red reverse)%d%Creset %Creset%s %C(cyan)%b'
    sync = !git checkout master && git pull origin master && git fetch -p origin && git branch -d $(git branch --merged | grep -v master | grep -v '*')
    co = "!f() { args=$@; if [ -z \"$args\" ]; then branch=$(git branch --all | grep -v HEAD | fzf --preview 'echo {} | cut -c 3- | xargs git log --color --graph' | cut -c 3-); git checkout $(echo $branch | sed 's#remotes/[^/]*/##'); else git checkout $args; fi }; f"
    di = "!f() { args=$@; [ -z \"$args\" ] && args=HEAD; ([ \"$args\" = \"HEAD\" ] && git status --short || git diff --name-status $args | sed 's/\t/  /') | fzf --preview \"echo {} | cut -c 4- | xargs git diff --color=always $args --\" --multi --height 90% | cut -c 4-; }; f"
    stl = !git stash list | fzf --preview 'echo {} | cut -d: -f1 | xargs git stash show -p --color=always' --height 90% | cut -d: -f1
    sta = !git stl | xargs git stash apply
    std = !git stl | xargs git stash drop
[pager]
    log = diff-highlight  | less -R
    show = diff-highlight | less -R
    diff = diff-highlight | less -R
```
#### 2. alias 설명
git 단축키 설명.
 - git lg는 과거 로그 이력을 보여줌.
 
 - git dlg는 과거 좀 더 디테일한 로그를 보여주어 변경 사항을 확인해서 리뷰에 도움을 줌.

 - git mlg는 머지된 로그만 보여줌.

 - git ls는 커밋 로그를 트리형태로 이쁘게 보여주고 해당 커밋 로그를 선택하면 상세 정보가 보임

![git_ls_resize](https://github.com/user-attachments/assets/02b0e857-3626-4391-8313-21143065fd38)

 
 - git sync는 현재 master를 최신 상태로 한 후 branch를 삭제함. branch를 삭제하기에 유의해야 함.

 - git co는 원격을 포함한 branch 목록에서 선택한 브랜치를 checkout 할 수 있게 함

![git_co_resize](https://github.com/user-attachments/assets/4775c2b6-1ba6-4e14-89eb-784fd642a749)

 - git di는 소스의 차이를 파일마다 전환하면서 확인할 수 있게 해줌

![git_di_resize](https://github.com/user-attachments/assets/9a793d4a-ea04-4dc2-b16a-409465fcff42)

 - git stl은 stash 목록에 변경 내용을 포함하여 확인할 수 있음

![git_stl_resize](https://github.com/user-attachments/assets/9ac2648e-d9c5-47a0-982b-ece1798fbd7f)


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

#### 4. pager 설명
diff-highlight 행수준 diff 뿐만 아니라, 단어 수준에서의 diff도 강조되고, GitHub의 diff처럼 보여준다.

#### 5. gitignore 설정
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

#### 6. 커밋 메세지
커밋 메세지는 가독성, 개발한 내용을 잘 이해하도록 메세지를 작성해야 한다. 메세지 내용엔 주로 어떻게보다 무엇을, 왜에 초점을 맞추어 작성하고, 내용 앞에 아래의 프리픽스 추가해서 커밋 메세지의 이해도를 높이자.

 - 프리픽스 정의(추후 의견을 통해 추가될 수 있음).
```
feat: 새로운 기능 추가일 경우
fix: 버그 고친 경우
docs: 문서작성 및 갱신의 경우
style: 코드 포멧 변경의 경우
refactor: 리팩토링의 경우
perf: 성능 개선의 경우
test: 테스트 코드 추가 및 변경의 경우
```

[git_lg_google]: https://lh3.googleusercontent.com/ISo1HypVtwH6dEVCc9xXEK1OGCF6OvSmOBtUheLp70hkex3_N9cvG1uEDoZW3vllf5Cu4BnLIF6vI1sTjKLA5NIJ1f-mp6ChwC0m390ePB4onGTfEl5XVnAJwiGk5NjEQMHU-exhTK-CmgFz9H8h6zs4NrPGV67ZdIkJ4LjNvjWWkZRgoRFAq9-K8irLVD3NfdCQNPreWwGa3uOnPae6z1johka5s10HNIG2VlMiDe8Irlgn5Hvmqp8Dl4OEfN0yFPsF2tTLouedagVBr8o6zUcRvMWdqonKTcTeAXh0sEJRorwYHCpll2nNYS3-WLrDGlNlUHa0jY6DdyCm9PeqpKLWPjdB5Mn0XUzIW7ZXADJbLVlruwxwyWRjYes_beBCTUt8jCklAPTPMmHXqcXBXc0pcDVkek4IjSAQxaPTWpE946qgoWgaC4mpAAHHM_-FA1svKh3Nso5k_kHeHhMpj1xslhWft1Ig9yENfifdsRCzKgfqY93nfY3UfAGm2I_eQuZfh-x75jeOEft6INPY3_Niqn03c7eoE0XUXgV7j6Auyqca1mg6_zOh1o9nsuRpacziczv4eBmGwiqI4oPHLOlY-TGwyJ3igyE_e3roCR73LE-93HiptDIQttjGTq-g0uQAGZSrx1YGA9quENPncLscF1TuS4ZnKtcP03hoCwe1ym_kItva4g=w600-h361-no
[git_ls_resize_google]: https://lh3.googleusercontent.com/mosfGbi00MhOHolcaYDvTo-geKtaD4f6SdKE4H8st1lKePfqcUw6uScdOBFlF9j8UQ5NtYhOqshzLiGBO0s9dzwakIyykS8K47hjZtEj1OuhjC-oajHozvGKqkfcYCObSHQlFZgJgzuL_79ix_iFPIRB56Hs1ZdJGeuBS7ZO4E3-olov3VkGumRnYYgMKdlwXHwBtCaFmkFsa8giFUHq4Jp4hB3Fi7DJndaq0TYTirATjsgW62G7CMtg6DCtRYVaZx8X0WZbL47No1PlOyQBnWWiH4tYgbtm4nHza-MkLI0JqM-TOJ7n0_nXzUNFqgJEXta5hFwNS1rsCof5_QWveFR4p78zxHfzrI48SCfwnh7NTubxXfiYX-Oh7VmIbyJBfuA4_W16XE1JHgXgPR68wB4_D3iAW0l8mCDm7oJt-w1dUnM4HRLSbRo0KZWzkGUqzGuB_FC91tLRGcPWobswf9fDovnUU4CrxcoEEDQVISWgIO4uyt5i3mC0crbeoN-VfFFeEWqWwXcZnLxv0fnPTKfYkzZFurm_WO0_N-zkXjLqHZQxsRn3a8vN-vlauQGC2wdM1rvuybEjO9-i2q_IfrlBBSiYZTt2gir-EaRSJw5zZ4VQB2tiMextnExrqBBGcGRck-rKg1XAzRr835ehSreWADCUVcU73KTDRyrfeVy33jvn8kYMcw=w600-h373-no
[git_co_resize_google]: https://lh3.googleusercontent.com/BXhd3PCZlloNoaGboNhjqac0KIhaekqCTMiD_vkn4GdOnfjB7frgmG6Y92_zk_2ldOVihik_49xk-nXwlHOvolPPfPP9xoHw2T2akrphAI1eq9OiQ0tfVaVuB5Q15BMj5-bmKmJcEr242NIP0lCH4RL6Szn3BITZePvD5hiywJqmveSyHqf2SnWwHbNGB3Rq6Op9ZXxjpf1im9hzmtRn2youHTH92FHCmZZVX87V2BRhMZD3kqWEFLlDAPpKaEIGw-wfH1FcqL5yfIF8opkgMBcrhilwKOJWuy9I8llMyVMlQ1iVnQtLPSKnIT0DP4wwFGA8NkmkmWhzb8gOButoplqONXzvIV4sXgpEUvAUG9LcAc01B7pP4NKNk5KmcNzaooPK9-l8_mcg2BOqlwcTkbhT85gjVuNt_GiwSd_yxxxRWj6HAG9CQESm9KBbXyrrz9bRqxIRhrXRwZhQb7Wr3e0m0eRz9KhtUJ26sJrdVWuRBRUqaGq7v-iYxxcz6xxCp-bzL1zlL2QVl4g2iq1AyR70WGlaN90K4v6pe64mYNUq1dFRL-xn6FZTQK6P0cpjwUg0Mm5FOkiBBhuKc95NL7ew8v2WuQhOejhvcGMNLhynCBSHXbixwwwFx10CTMFG4aR938N3OJKNvmkpO5H8N2e-ls2UzJrdrYSzKcaUjBoNXgGt_C-KSA=w600-h336-no
[git_di_resize_google]: https://lh3.googleusercontent.com/Ca9fx46bXVggPQSLsGjcJC5qRpUj-UoJ8hfh5ZdCn1-wPSVpiLEgdG8LUu_wF9O7XU2SlhwIn9JjgwNMORgk320VvrRu6VN4xHjcvZGu3elYe16cb-StHdi7IFkZIVLw-CO5FAdAP32Btf0Wqkz4N72ap_-W3WT1mC9hL-fAyPWK80yNNGnvA9_mtKNl1IbtH7dBcw9lDE12udHyb_R_DXGqgBwWTu6XedU2sfOIZ_HdNz_cworAOTT3NSWn16Gi1LoHzga0rqoZFC-c_g83mzNkVUVgg7cSrpzZMVU4jEij6iCu7Tl4E4VDM20P2xPzHjgWbKoVlw9W2-R0GB8_-ZV9VPCiJfM7XQcirQOC8FxUZGl3tGz-Bjh-kr5ReEQmdIJCpns023CgC5JTDq6Y4sF6glYDnKhZFtkkNOCS0dBrmSfQ8sFuspRvwDsOzX8sVYwZtbg4GWfFIc6oYN2jhFXusos4b-YpIf0f769jtCLwCNoaUXWf1jR6Ewb1_RJgyJu58DJJ7UPppoYT2pjlO_ydeQjobmxt_dxRT63f8sDVuK4Lt9rSxn4B3sBOMVLu59eOZhTijR2juUhikVX_4dLi6j6m9RkmKsdBf8tlLDbrTJK9mJv5RRShZ0JqxSoywnehR_1kTPep5Jcqo98BvT440V4ywFqb1D5ZI_BkI5_Uy9PK-MtV3w=w600-h359-no
[git_stl_resize_google]: https://lh3.googleusercontent.com/YeKOKgpMh-RppIM00yKNQ6umqfxpnaXJVWzws1eW0AKtKn9ouT-gh0kTi_26j_3BjIC4tOU8WIODQWsqiTV9nEejgnEaLGhtsoegA6AW6HrH8lm__TWwGRc9oRmhFHauSgJ-zdT_okIggDC54_T-Htj6-Vun3dbNWtmyziPn4JMQT8PqgmjvhqnsZySXAXihtFm6TgFY9UesSv4oTzR4Qjg7zY89BJshl-3Q37Y_wl24VsMGShhBYFtCL5JdvTo_av2EdOQ7HWywWQEZhOzpd4uztAeTAUgQOFFXg9f8XwVVSmaqwMXa8hzIpAbJY0zCaT2lEz-WZUrS51DI7pIFBFneiZMirJUWaxxmB30sJG_DYfJJ58MOF85XRF8cuWwa9P7IA3XYjGTOg79xvelg3P9DcNXLTZcmH9JgjxgOylmyG_X8ZPdZY5XTs8MgW38moFzZKaOY-ISxgKBWVR3jbmPzP5OH_0fCUP1v19uZ9Kx42ZBj2Od1ARcCkv62V_l9S5AkgTD1T_29I06ijoZNpiSvYK7TlmdCZfTSvuvPC2lOWKvAPJjIQOPdw2_iPbEghyskfn0u6JSQs1eqpxhBjB8SaZU_cRRi0qv6xSmSUkc_g7nQYRGeZhbbFdXqo80x5QBiQR-6OB2QYVs9TuXU_nOMctBSEhRakKqHIG6Bc3BTkHR8YiwG6w=w600-h336-no
