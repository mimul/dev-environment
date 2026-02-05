Trello, Github, Slack을 활용한 개발 프로세스
======================
개발 흐름
---------------
![사진 1][dev_process]
[dev_process]: http://www.mimul.com/pebble/default/images/blog/Projects/dev_process.png

개발 프로세스(Trello, Github, Slack)
---------------
#### 1. Trello Card 만들기
1.1 기본적인 Trello 흐름
+ `Trello에서 개발해야 할 기능을 [To Do(Story)]라는 이름의 리스트에 카드로 만든다.`
+ `해당 스토리(카드)를 개발자가 구현에 들어가면 [Doing(WIP)] 리스트에 카드를 옮긴다.`
+ `리뷰에 들어가면 [Review(Sprint1)] 리스트로 옯기고`
+ `개발 브랜치가 병합하여 테스트를 완료하면 [Done(Sprint1)] 리스트에 카드를 옮기고 해당 기능을 클로즈한다.`

![trello](https://imgur.com/BwOKpER)


1.2 Trello 카드 내용은 Description란에는 이슈 링크를 걸어주거나 Wiki 링크를 걸어줘, 해당 스토리의 정보를 알 수 있도록 해준다. 그리고 Spec을 참조하여 Checklist를 추가해 완료조건을 기술해 개발해가면서 하나씩 처리해 나간다.

![사진 3][trello_card]

1.3 카드 처리 및 이동시 Slack Alert를 줘 실시간으로 처리가 가능하다.

![사진 4][trello_alert]

[trello]: http://www.mimul.com/pebble/default/images/blog/Projects/trello.png
[trello_card]: http://www.mimul.com/pebble/default/images/blog/Projects/trello_card.png
[trello_alert]: http://www.mimul.com/pebble/default/images/blog/Projects/trello_alert.png

#### 2. Branch 만들고 Pull Request, Merge 하기
2.1 소스 리모트와 동기화
```
> git checkout master && git pull origin master && git fetch -p origin
```
2.2 브랜치명을 만들고 브랜치로 이동
```
> git checkout -b dev_standard
```
2.3 작업 후 커밋
```
> git commit -a -m "[VOY-201] README git 사용법 추가"
# 커밋 작성은 Issue ID를 넣고 내용은 구체적으로 제시한다.
```
2.4 작업을 완료 후 master branch로 변경하여 remote에 새로운 변경 사항을 master에 반영
```
> git checkout master
> git pull origin master && git fetch -p origin
```
2.5 변경사항이 있다면 dev_standard branch에서 rebase를 수행
```
> git checkout dev_standard
> git rebase master
```
++ rebase 중 충돌이 발생하면 아래 수행하고 아니면 넘어감. ++
```
> git add .
> git rebase --continue
```

2.6 Pull Request 요청하기 전에 Trello 카드도 [Review] 리스트로 옮기고 Checklist 하나를 더 만들어서 Review 내용(리뷰 담당자 포함)을 기술하면 Trello 알림을 통해 담당자가 리뷰어임을 알게 된다. 아니면 Trello 카드를 만들때 Review Checklist를 미리 만들어서 리뷰 대상자를 등록한다.

2.7 Pull Request 요청
- 아래 커맨드로 Pull Request 요청을 한다.
```
> git push origin dev_standard
```
- Push 후 Github의 Repository로 이동해서 Compare & pull request 버튼 클릭하고, 코멘트[To close VOY-201(Jira Issue 번호), 혹은 Github Issue 사용하면 #1234로] 남기고 Create pull request 버튼 클릭한다.

	![create_pullrequest](https://imgur.com/qJrfKKW)]

- 이때 Pull Request 정보가 Slack을 통해 담당자에게 보내지게 된다.

	![slack_pullrequest](https://imgur.com/92B26Ty)]

2.8 리뷰 수행
- 리뷰어는 file change 버튼을 눌러 master 브랜치와의 소스 차이를 보면서 리뷰어의 의견이 있을 경우 소스 중간에 원하는 자리에 코멘트를 남긴다.

	![사진 7][git_review]

- 리뷰어는 코멘트나 리뷰 회의를 통해 나온 수정사항을 반영해서 다시 push를 한다.
```
> git commit -a -m "README 파일의 기술 아티클 문구를 기술 블로그로 수정"
> git push origin dev_standard
```

- 수정한 푸시도 Pull Request에 반영된다.

	![사진 8][git_push]

2.9 병합(Merge)
- 리뷰가 완료되면 병합은 두가지 방법으로 진행할 수 있다. 첫번째 방법은 리뷰가 완료되면 Github에서 Merge Pull Request를 클릭하고 Confirm merge 버튼을 클릭해서 Merge를 완료할 수 있다.
- 두번째 방법은 로컬에서 커맨드로 병합을 완료할 수 있다.
```
> git checkout master
> git merge --no-ff dev_standard
```
- Merge가 완료되고 해당 Pull Request가 Close 된 것을 확인할 수 있다.

	![사진 9][git_merged]

- Github에서 Delete Branch 버튼을 클릭하거나 아래 로컬에서 커맨드로 원격 브랜치를 삭제한다.

```
> git push origin :dev_standard
```


2.10 로컬 Master 동기화 및 로컬 브랜치 삭제
- 로컬 master 동기화
```
> git checkout master && git pull origin master && git fetch -p origin
```
- 로컬 브랜치 삭제
```
> git branch -d dev_standard
```

[slack_pullrequest]: http://www.mimul.com/pebble/default/images/blog/Projects/slack_pullrequest.png
[create_pullrequest]: http://www.mimul.com/pebble/default/images/blog/Projects/create_pullrequest.png
[git_review]: http://www.mimul.com/pebble/default/images/blog/Projects/git_review.png
[git_push]: http://www.mimul.com/pebble/default/images/blog/Projects/git_push.png
[git_merged]: http://www.mimul.com/pebble/default/images/blog/Projects/git_merged.png

#### 3. 배포하기
- 테스트 코드를 돌리고, Jenkins나 자체 배포 도구를 활용하여 운영 서버에 소스를 배포한다.

#### 4. 배포후 확인
- 기능 테스트를 눈으로 확인하면서 화면의 깨짐, 데이터의 정확성, 브라우저 호환성 등을 점검한다.
- Selenium 도구를 통해 브라우저단에서 테스트를 할 수 있는데, 이 Selenium이 구동한 브라우저의 결과 화면을 아이컨텍해 봄으로써 어느 정도 테스트 자동화를 할 수 있다.

#### 5. Trello 카드 Done 리스트로 이동
- 배포후 확인에서 이상이 없다면, Trello의 카드를 [Done(Sprint1 - 날짜기간)] 리스트에 이동시키고, 이슈를 Close한다.

License
---------------
Copyright &copy; 2015 (주) 와이즈에코.
Licensed under the [Apache License, Version 2.0][Apache]
Distributed under the [MIT License][mit].
Dual licensed under the [MIT license][MIT] and [GPL license][GPL].

[Apache]: http://www.apache.org/licenses/LICENSE-2.0
[MIT]: http://www.opensource.org/licenses/mit-license.php
[GPL]: http://www.gnu.org/licenses/gpl.html
