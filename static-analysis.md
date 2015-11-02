코드 정적 분석 가이드
======================
개발자가 코드를 작성함에 있어서

- 기존에 공유된 코딩 스타일을 위반하거나,
- 잠재적 버그의 가능성이 있는 코드들,
- 클래스나 패키지간의 종속성 등 설계상의 문제가 되는 코드들,
- 중복된 코드

등의 문제를 쉽게 찾아주는 정적 분석도구를 잘 활용해 리팩토링하기전에 최소한의 코드 품질을 유지하는 것을 보장하고자 이 문서를 작성한다. 기본 환경은 Java, Javascript 언어에 한정한다.

Checkstyle
---
코딩 스타일에서의 벗어난 코드들을 알려준다.

###설정

[wiseeco-checkstyle.xml][wiseeco-checkstyle] 파일을 프로젝트 ROOT에 다운로드한 다음, Checkstyle-IDE 플러그인은 로딩 이슈가 있어서 설치를 제외하고, pom.xml에 아래 사항을 기술해 준다.
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-checkstyle-plugin</artifactId>
    <version>${checkstyle-version}</version>
    <configuration>
        <configLocation>wiseeco-checkstyle.xml</configLocation>
    </configuration>
</plugin>
```
intellij의 Maven Project탭에서 Plugins아래 checkstyle에서 checkstyle:checkstyle을 실행하면 결과를 보여준다. 결과중에 Warning을 하나씩 잡아가면 된다. 실행 주기는 커밋하기 전에 한번 실행해서 확인한다.

###설정 정보
1. [blocks](http://checkstyle.sf.net/config_blocks.html)
    ```
    AvoidNestedBlocks : 중첩된 블록 체크.
    EmptyBlock : 빈 블록 체크.
    LeftCurly : 중괄호 {의 위치 체크.
    RightCurly : 오른쪽 중괄호 }의 위치 체크.
    ```

2. [Coding](http://checkstyle.sourceforge.net/config_coding.html)
    ```
    EmptyStatement : 빈줄 확인.
    HiddenField : 동일한 클래스 내에서 정의된 필드와 같은 이름의 지역 변수와 메소드 매개 변수가 존재하는지 여부를 체크.
    IllegalInstantiation : 잘못된 인스턴스 사용 체크.
    ModifiedControlVariable : for 문의 증감 부분에 설명 된 변수를 블록으로 변경하고 있는지 여부 체크.
    SimplifyBooleanExpression : 중복 연산 체크.
    IllegalCatch : 부적절한 Catch 사용 체크.
    FallThrough : case문에 break문이 정의되지 않은 부분 체크.
    ```

3. [imports](http://checkstyle.sf.net/config_import.html)
    ```
    AvoidStarImport : .* 형식의 import 문장을 체크.
    IllegalImport : import하면 안되는 패키지 체크.
    RedundantImport : 중복 import 문을 체크.
    UnusedImports : 프로그램에서 사용되지 않는 import 문을 체크.
```

4. [Naming Conventions](http://checkstyle.sf.net/config_naming.htm)
    ```
    ConstantName : 상수(static, final)의 명명 규칙을 설정. 기본값은 ^[A-Z][A-Z0-9]+(_[A-Z0-9]+)*$.
    LocalFinalVariableName : final 로컬 변수 명명 규칙을 설정(카멜 표기법). ^[a-z][a-z0-9][a-zA-Z0-9]*$.
    MemberName : static이 아닌 필드(변수) 명명 규칙을 설정(카멜 표기법). ^[a-z][a-z0-9][a-zA-Z0-9]*$.
    MethodName : 메소드의 명명 규칙을 설정(카멜 표기법). ^[a-z][a-z0-9][a-zA-Z0-9]*$.
    ParameterName : 매개 변수의 명명 규칙(카멜 표기법). ^[a-z][a-z0-9][a-zA-Z0-9]*$.
    StaticVariableName : static변수 명명 규칙을 설정(카멜 표기법). ^[a-z][a-z0-9][a-zA-Z0-9]*$.
    TypeName : 클래스나 인터페이스의 명명 규칙을 설정(파스칼 표기법). ^[A-Z][a-zA-Z0-9]+$.
    PackageName : 패키지의 명명 규칙을 설정. ^[a-z]+(\.[a-z][a-z0-9]*)*$.
    ```

5. [whitespace](http://checkstyle.sf.net/config_whitespace.html)
    ```
    MethodParamPad : 메소드 정의 부분(호출 부분도 포함)의 공백을 체크.
    ```

FindBugs
---
알려진 버그 패턴에 따라 잠재적 버그를 검사해 알려준다.

###설정
[findbugs-exclude-filter.xml][findbugs-exclude-filter] 파일을 프로젝트 ROOT에 다운로드한 다음, pom.xml에 아래 사항을 기술해 준다.
```
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>findbugs-maven-plugin</artifactId>
    <version>${findbugs-version}</version>
    <configuration>
        <excludeFilterFile>findbugs-exclude-filter.xml</excludeFilterFile>
        <!--<includeFilterFile>findbugs-include-filter.xml</includeFilterFile>-->
    </configuration>
</plugin>
```
intellij의 Maven Project탭에서 Plugins아래 findbugs에서 findbugs:findbugs실행 후 findbugs:gui를 실행해서 Warning들을 처리해 나간다. findbugs 실행 주기도 커밋 시점에 한번 실행해서 확인한다.

###오류메세지 정보
1. Correctness
    ```
    Possible null pointer dereference in method on exception path : 예외가 발생했을 경우에 null 포인터가 참조될 가능성 있음.
    Nullcheck of value previously dereferenced : 중복 널 체크.
    Uninitialized read of field in constructor : 생성자 초기회 이전에 필드를 사용하고 있음.
    Invocation of toString on an array : 배열을 toString하고 있음.[C@16f0472 같은 의미없는 문자열)
    ```

2. Bad practice
    ```
    Method ignores results of InputStream.read() : InputStream.read() 메소드의 반환 값을 무시하고 있음.
    Method names should start with a lower case letter : 함수명이 카멜케이스가 아님.
    Method may fail to close stream : 메소드에서 생성된 스트림이 close되지 않고 종료될 가능성이 있음.
    ```

3. Experimental
    ```
    Method may fail to clean up stream or resource on checked exception : 메소드에서 생성된 스트림이나 리소스가 close되지 않은 채 메소드가 종료 될 가능성이 있음
    ```

4. Internationalization
    ```
    Reliance on default encoding : 디폴트 인코딩 지정 안했을 경우. String.getBytes(), new String(bytes) 등 인코딩 정보를 빠진 경우 디폴트 인코딩 정보 등록해야 함.
    ```

5. Multithreaded correctness
    ```
    Call to static DateFormat : DateFormat 객체의 사용은 멀티 스레드에 안전하지 않음.
    ```

6. Performance
    ```
    Boxed value is unboxed and then immediately reboxed : Box된 값이 바로 Unbox 되는 부분이 있음.
    Boxing/unboxing to parse a primitive : primitive 타입을 강제 Box, Unbox 한 부분이 있음.
    Unread field : 읽혀지지 않는 필드가 존재함.
    ```

7. Dodgy code
    ```
    Result of integer multiplication cast to long : 정수(int)의 곱셈 결과를 long으로 변환함.
    Dead store to local variable : 로드되지 않는 로컬 변수에 쓰기 작업을 수행하고 있음.
    Parameter must be non-null but is marked as nullable : 파라미터는 널이 아니어야하는데 널일수도 있음.
    Check for oddness that won't work for negative numbers : 음수에 대해서는 동작하지 않을수도 있음.
    Exception is caught when Exception is not thrown : Exception이 발생하지 않는 곳에서 Exception을 캐치하고 있음.
    Switch statement found where default case is missing : default문이 없음.
    Useless object created : 의미없는 객체 생성문이 있음.
    Dead store of null to local variable : 로컬 변수에 null을 할당해서 GC대상이 되게 하는 방법은 java 6.0이상에서는 더이상 불필요함.
    ```

[ESLint](http://eslint.org/)
---
Javascript의 알려진 버그들을 알려준다.

###ESLint의 특징
- 모든 규칙을 자유롭게 on/off 할 수 있음.
- 자신의 프로젝트에 맞게 사용자 정의 규칙을 만들어서 사용 가능.
- 풍부한 내장 규칙 이외에 많은 플러그인이 공개되어 있음.
- ECMAScript 2015(ES6)를 지원.
- React의 JSX 기법을 지원.
- Babel과 연계하여 ECMAScript 2016(ES7) 이후의 구문과 Flow형 주석에도 대응함.
- 지속적으로 소스 업데이트가 진행되고 있음.

###설치 및 사용법
```
> npm install -g eslint
> eslint  -v
> eslint --init
{
    "rules": {
        "linebreak-style": [
            2,
            "unix"
        ],
        "semi": [
            2,
            "always"
        ]
    },
    "env": {
        "browser": true
    },
    "extends": "eslint:recommended"
}
> eslint src/main/webapp/resources/app/common/utils/helpers.js
```
"extends": "eslint:recommended" 라인만 있어도 소스 분석해 문제점을 나열해 준다. 실행주기는 커밋시점에 한번 실행하고 위의 마지막 라인에서처럼 변경된 javascript파일을 실행해서 점검한다.

###설정 정보
1. Possible Errors
    ```
    comma-dangle : 마지막 쉼표있을 경우 경고.(예: [1, 2, 3,])
    no-cond-assign : 조건식안에 대입식이 있을 경우 경고.(예 : if(a = 10) {})
    no-console : 소스에 console 로그가 있으면 경고.
    no-constant-condition : 조건식이 상수라면 경고.(예 : if (true) {})
    no-control-regex : 정규 표현식 중에 ASCII 제어 문자가 존재하면 경고.
    no-debugger : debugger 문 경고.
    no-dupe-args : 인수 이름이 중복되면 경고.(예 : function foo (a, a) {})
    no-dupe-keys : 속성 이름이 중복되면 경고.(예 : var foo = {a : 0, a : 1};)
    no-duplicate-case : case 절이 중복되면 경고.(예 : switch (foo) {case 0 : case 0 :})
    no-empty-character-class : 정규 표현식에 널문자 집합이 존재하면 경고.(예 : var foo = /abc[]/;)
    no-empty : 빈 블럭이 있으면 경고.
    no-ex-assign : catch 절에 예외 변수에 다시 대입문이 있으면 경고.(예 : try {} catch (err) {err = 0;})
    no-extra-boolean-cast : 조건식 안에 !!연산을 경고.(예 : if (!!a) {})
    no-extra-semi : 불필요한 세미콜론을 경고.(예 : var a = 0;;)
    no-func-assign : 함수에 대입하면 경고.(예 : function foo () {} foo = 0;)
    no-inner-declarations : 함수 이외의 블록 안에 함수 선언을 경고.(예 : if (a) {function foo () {}})
    no-invalid-regexp : RegExp 생성자에 잘못된 문자열을 작성하면 경고.(예 : new RegExp ("[/");)
    no-irregular-whitespace : 부적절한 공백을 경고.
    no-negated-in-lhs : in 연산자의 좌변 값이 예기치 않은 연산자인 경우에 경고.(예 : if (! a in foo) {})
    no-obj-calls : 함수가 아닌 내장 변수에 대한 함수 호출을 경고.
    no-regex-spaces : 정규식 파싱동안 연속적인 공백이 존재한다면 경고.
    no-sparse-arrays : 스파스 배열 리터럴을 경고.(예 : var a = [0, 1,, 3];)
    no-unreachable : 도달할 수없는 코드에 경고.(예 : function foo () {return; var a = 0;})
    use-isnan : NaN 과의 비교를 경고.(예 : if (foo == NaN) {})
    valid-typeof : typeof 비교를 경고.(예 : if (typeof a == "strong") {})
    ```

2. Best Practices
    ```
    no-fallthrough : switch 구문 fall through를 경고.(예 : switch (a) {case 0 : foo(); default: bar();})
    no-octal : 8진수 리터럴을 경고.(예 : var a = 0755;)
    no-redeclare : 변수의 재 선언을 경고.(예 : var a = 0; var a = 1;)
    ```

3. Variables
    ```
    no-delete-var : 변수에 delete 식을 경고.
    no-undef : 정의되지 않은 변수에 대한 참조를 경고.
    no-unused-vars : 선언했지만, 사용되지 않은 변수를 경고.
    ```

4. Stylistic Issues
    ```
    no-mixed-spaces-and-tabs : 공백과 탭이 혼재하고 있는 경우에 경고.
    ```

[wiseeco-checkstyle]: https://github.com/mimul/dev-environment/blob/master/build-config/checkstyle/wiseeco-checks.xml
[findbugs-exclude-filter]: https://github.com/mimul/dev-environment/blob/master/build-config/findbugs/findbugs-exclude-filter.xml