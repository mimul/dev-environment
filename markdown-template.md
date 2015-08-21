프로젝트 제목
======================
여기에 프로젝트 개요를 작성합니다.

단락을 나눌때는 빈라인을 넣으면 됩니다.

사용법
---------------
### 인라인 코드
인라인 **code**는 `Backtick`(` ` `)으로 묶는다.

### 블럭 코드
```java
	public static void deleteCookie(HttpServletResponse response, String cookieName) {
		try {
			setCookie(response, cookieName, null, 0, "/", null);
		} catch (Exception e) {
			log.error(e.getMessage());
			throw new RuntimeException("Exception generated.", e);
		}
	}
```

### 리스트

* Lists must be preceded by a blank line (or block element)
* Unordered lists start each item with a `*`
- `-` 간격이 조금 벌러진다.
	* indent 중첩 리스트.
		1. 순서 리스트.
		2. `1. ` 방식으로 숫자를 넣고 문단의 시작하면 된다.
		42. `42`를 써도 순서에 의해 3이 보여진다.
		1. So you might want to start each line with `1.` and let me sort it out

### 인용

> Angle brackets `>`이 인용에 사용된다.
단락사이가 공백이 아니면 `>`를 앞에 붙일 필요는 없다.
> Looks kinda ugly though.
> > 중첩 인용.
> > > 한번더 중첩.
>
> 인용문 안에서도 마크다운 문법이 적용된다.
>
> * 리스트.
> * [Links][arbitrary_id]
> * Etc.

[arbitrary_id]: http://mimul.com "Title"

### TeX-like Math Syntax
I can also render TeX-like math syntaxes, if you allow me to.[^math] I can do inline math like this: \\( 1 + 1 \\) or this (in MathML): <math><mn>1</mn><mo>+</mo><mn>1</mn></math>, and block math:

\\[
    A^T_S = B
\\]

or (in MathML)

<math display="block">
    <msubsup><mi>A</mi> <mi>S</mi> <mi>T</mi></msubsup>
    <mo>=</mo>
    <mi>B</mi>
</math>

### Task List Syntax
1. [x] I can render checkbox list syntax
	* [x] I support nesting
	* [x] I support ordered *and* unordered lists
2. [ ] I don't support clicking checkboxes directly in the html window

매개 변수 설명
---------------
```java
    public static void deleteCookie(HttpServletResponse response, String cookieName)
```
+   `response` :
    _Http응답객체_ 는  쿠키 삭제시 사용.

+   `cookieName` :
    _쿠키명_ 은 삭제할 쿠키명

API설명
---------------
### API 목록
| 서비스  | 카테고리  | 설명                    | 관련 DB    |
| :---- |:-------:| ---------------------: | :-------- |
| 블로그  | 방문자    | PV, UV 등 방문 트래픽 조회 | access_log|
|       | 로그인    | 로그인 트래픽 조회         | login_log |
### API 규약
##### 방문자(/visits)
| 파라미터    | 설명          | 값 예제    | 필수여부| 비고                          |
| :-------- |:------------:| --------:| :-----| :----------------------------|
| dateType  | 날짜구분       | day      | 필수   | day, week, month 값이 올 수 있음.|
| startDate | 검색조건 시작일자| 20150524 | 필수   | YYYYMMDD                      |
| endDate   | 검색조건 종료일자| 20150529 | 필수   | YYYYMMDD                      |
##### 로그인(/logins)
| column | column |
|--------|--------|
|  test  |  test  |

관련 정보
---------------
### 목록
1. [미물 블로그 : 쿠키 삭제 방법](http://mimul.com/)
    ![사진 1](https://avatars0.githubusercontent.com/u/6541847?v=3)

2. [깃허브 외부 소스 참조][link]
    ![사진 2][image]

  [link]: http://github.com
  [image]: https://avatars2.githubusercontent.com/u/9919?v=3

### 참조
> 어디어디에서 인용했습니다.
>
> > 공간을 두고 `>` 를 넣으면 인용에 인용을 할 수 있습니다.

~~불필요한 내용입니다.~~

License
---------------
Copyright &copy; 2015 (주) 와이즈에코.
Licensed under the [Apache License, Version 2.0][Apache]
Distributed under the [MIT License][mit].
Dual licensed under the [MIT license][MIT] and [GPL license][GPL].

[Apache]: http://www.apache.org/licenses/LICENSE-2.0
[MIT]: http://www.opensource.org/licenses/mit-license.php
[GPL]: http://www.gnu.org/licenses/gpl.html