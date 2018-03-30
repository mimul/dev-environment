리팩토링에 필요한 것들.
======================
리팩토링은 코드의 가독성 증대, 버그 감소, 확장성 고려 뿐만 아니라, 성능에도 영향을 줄 수 있는 중요한 행위입니다.
> 어떤 프로그래머도 컴퓨터에 이해할 수있는 코드를 쓸 수있다. 그러나 우수한 프로그래머 만이 인간을 이해할 수있는 코드를 작성할 수있다.
> From Martin Fowler

리팩토링의 기준점들
---
여기서는 리팩토링을 해야하는 기준점들을 나열합니다.

- 리팩토링을 하는데에는 목표가 분명하게 있어야 한다. 그 목표는 정량적으로 산정할 수 있어야 하고, 비즈니스와 고객에 도움에 되는지가 먼저이다.
- 정량적인 목표를 먼저 생각하고 리팩토링을 해야지, 리팩토링을 하고 난 다음 정량적인 목표를 연결하려는 생각은 될 수 있으면 하지 않아야 한다. 그렇게 하면 리팩토링의 효과와 더불어 회사의 목표와 개인의 목표가 와전될 수 있다.
- 정량적인 지표들. 성능 향성, 고객 유입율 향상, 이탈율 감소 등.
- 오픈 소스의 도입을 통한 리팩토링의 경우
- 문제 해결
- AOP를 통해 종적 관심사와 횡적 관심사의 분리.
- 높은 응집도, 낮은 결함도
- 중복 최소화(Don't Repeat Yourself)
- 개방 폐쇄 원칙(Open-Closed Principal) : 소프트웨어 구성 요소들(클래스, 함수, 모듈)은 확장은 가능하게 수정은 불가능하게.
- 리팩토링을 시작하기 전과 후에는 단위 테스트를 실행 코드의 기능을 변경하지 않았는지 확인한다.
- 작은 리팩토링과 테스트의 조합을 반복한다. 큰 리팩토링은 하지 않는다.

리팩토링을 해야할 시점
---
- 유사한 처리를 3 회 쓰게되면 리팩토링. <- 중복 최소화(DRY)
- 기능을 추가할 때.
- 버그를 수정할 때.
- 코드를 리뷰하고 난 다음.

네이밍
---

- 정리중

리팩토링 가이드(Java)
---
#### 1. 클래스 추출
- 하나의 클래스에 멤버변수가 많은지 확인해서 줄이는 노력을 한다. 응집력을 높이는 효과를 발생시킨다.
- 단일 책임의 원칙(Single Responsibility Principle): 하나의 클래스는 하나의 책임만 가져야 한다
```
public class User {
    private String name;
    private String phone;
    private String email;
}
클래스 추출 코드
public class User {
    private Name name;
    private Contact contact;
}

public class Name {
    private String value;
}

public class Contact {
    private String phone;
    private String email;
}
```
- 클래스에 컬렉션을 래핑: 컬렉션을 자신의 클래스에 래핑해서 의도와 목적을 분명하게 함.
```
public class Login {
    private List <User> users;
}

컬렉션을 래핑 한 클래스를 만들고 행동을 정리.
public class Login {
    private Users users;
}

public class Users {
    private List <User> users;
    // Collection 작업을 정리
}
```

- 파라미터 객체화: 긴 파라미터는 객체화해서 공통화하고 필요에 따라 특성이 있는 파라미터는 상속 형태로 다른 객체로 분리한다.
```
public List<Statistic> getGrid(String startDate, String endDate, String dimension, String measure, int serviceCode) {
    // do something
}
파라미터 객체화
public List<Statistic> getGrid(Report report) {
    // do something
}
public class Report implements Serializable {
    private static final long serialVersionUID = 5966853281756014048L;

    private String startDate;
    private String endDate;
    private String dimension;
    private String measure;
    private int serviceCode;
}
```

- 클래스와 패키지의 규모를 적정하게
  - 패키지안의 클래스는 공통 재사용 원칙(CRP)을 따르도록 한다. 연관된 클래스들 끼리 묶고 그안의 클래스들끼리는 재사용되어야 한다.
  - 클래스의 라인은 200줄 이상 되면 여러가지 일을 하고 있는 것으로 보고 단일 책임의 원칙(SRP - 하나의 클래스는 하나의 책임만 할당)에 따라 그 이하가 되도록 노력한다.
  - 패키지의 경우 15개 이상의 파일이 넘을 경우 목적에 맞게 패키지를 분화시키며 폐쇄성 공통의 원칙(CCP)을 따를려고 노력한다.

#### 2. 메소드 추출
- 함수의 길이는 열려있는 편집기의 한 화면에 들어올 수 있는 크기로 하며, 적어도 80라인을 넘지 않으면 스크롤없이 한 화면에서 볼 수 있다. 이렇게하면 스크롤이 없으므로 코드 읽기 집중도를 흩트리지 않는다.
```
public void stampMethod() {
    StringBuilder s = new StringBuilder();
    for (int i = 0; i < 10; i ++) {
        for (int j = 0; j < 10; j++) {
            if (i == j) {
                s.append("■");
            } else {
                s.append("●");
            }
        }
        s.append("\r\n");
    }
}

2회 메소드의 추출한 코드
public void stampMethod() {
    StringBuilder s = new StringBuilder();
    for (int i = 0; i < 10; i++) {
        appendLine (s, i);
        s.append("\r\n");
    }
}

private void appendLine(StringBuilder s, int i) {
    for (int j = 0; j < 10; j++) {
        appendMark (s, i, j);
    }
}

private void appendMark(StringBuilder s, int i, int j) {
    if (i == j) {
        s.append("■");
    } else {
        s.append("●");
    }
}
```
#### 3. 변수 추출
- 복잡한 수식인 경우 목적을 나타내는 이름으로 임시변수에 할당해 가독성을 높인다.
```
if ((platform.tuUpperCase().indexOf("MAC") > -1) && (browser.toUpperCase().indexOf( "IE") > -1) {
    // do something
}
임시 로컬 변수
final boolean isMacOs = platform.tuUpperCase().indexOf( "MAC") > -1;
final boolean isIEBrowser = browser.toUpperCase().indexOf( "IE") > -1;
if (isMacOs && isIEBrowser) {
    // do something
}
```
- 불필요한 임시 변수 제거
```
@Override
public List<User> getUsersByCompanyId(Long companyId, User user) {
  List<User> users = userRepository.getUsersByCompanyIdRole(User.builder().companyId(companyId).userType(1).build());
  return users;
}
불필요한 임시 변수 제거
@Override
public List<User> getUsersByCompanyId(Long companyId, User user) {
  return userRepository.getUsersByCompanyIdRole(User.builder().companyId(companyId).userType(1).build());
}
```

#### 4. 가독성 있는 if
- 비교 대상을 왼쪽에 배치
```
if (age >= 10) {} <- 비교 대상을 왼쪽에 배치
```
- 범위 비교는 왼쪽은 작은 비교, 오른쪽 큰 비교
```
if (20 <= age && age <= 40) {}
```
- true/false는 비교 안함(중복비교애 해당)
```
if (user.isMale()) {}
```
- 삼항 연산자 활용
```
public String getGrade(int score) {
  if (90 < score)
      return "A";
  else
      return "B";
}
개선된 코드
public String getGrade(int score) {
    return (90 < score) ? "A" : "B";
}
```
- 불리언 리턴함수일 경우 직접 비교결과를 리턴(불필요한 로컬 변수 제거)
```
return foo == bar;
```
- NULL 체크 if문 제거
```
if (signup.getCompanyId() == null) {
  signup.setCompanyId(user.getCompanyId());
}
Optional 활용
signup.setCompanyId(Optional.ofNullable(signup.getCompanyId()).orElse(user.getCompanyId()));
org.apache.commons.lang.StringUtils.ObjectUtils 활용
signup.setCompanyId(ObjectUtils.defaultIfNull(signup.getCompanyId(), user.getCompanyId()))
```

#### 5. else문 없애기: 조건문이 중첩되어 았으면 코드 이해하기도 어렵고 스크롤 압박까지 있다면 가독성이 떨어지게되고 그러면 버그를 만들 확률을 높일 수 있다. 그래서 조기 반환 기법과 전력 패턴을 사용하면 해결할 수 있다.
- 조기 반환
```
public String getGrade(int score) {
    if (90 < score) {
        return "A";
    } else {
        if (60 < score) {
            return "B";
        } else {
            return "C";
        }
    }
}
조기 반환 처리 코드
public String getGrade(int score) {
    if (80 < score) {
        return "A";
    }
    if (60 < score) {
        return "B";
    }
    return "C";
}
```
- Strategy 패턴 활용: 전략 패턴은 처리 알고리즘이 서로 틀린 경우(변화)에 사용됨.
```
public class Calculator {
    private int type;

    public Calculator (int type) {
        this.type = type;
    }

    public int calculate (int a, int b) {
        int result = 0;
        if (type == 1) {
            result = a + b;
        } else if (type == 2) {
            result = a - b;
        }
        return result;
    }
}
전략패턴 활용 코드
public class Calculator {
    private Type type;

    public Calculator (Type type) {
        this.type = type;
    }

    public int calculate (int a, int b) {
        int result = type.execute(a, b);
        return result;
    }
}

public interface Type {
    public int execute (int a, int b);
}

public class Add implements Type {
    @Override
    public int execute (int a, int b) {
        return a + b;
    }
}

public class Subtract implements Type {
    @Override
    public int execute (int a, int b) {
        return a - b;
    }
}
```
#### 6. Boilerplate Code를 줄이기
- 컴퓨터 프로그래밍에서 상용구 코드 또는 상용구는 수정하지 않거나 최소한의 수정만을 거쳐 여러 곳에 필수적으로 사용되는 코드를 말한다. 이와 같은 코드는 최소한의 작업을 하기 위해 많은 분량의 코드를 작성해야하는 언어에서 자주 사용된다.
- Lombok 등을 활용해서 불필요한 코드를 줄이자. [Lombok 주의 사항](http://kwonnam.pe.kr/wiki/java/lombok/pitfall)
- 멤버변수가 많을 경우 Lombok @Builder annotation(Builder 패턴)을 활용해 멤버 변수 셋팅하자. 멤버 변수에 Default 초기화가 있을 경우 @Builder.Default를 선언해준다.
```
@Getter
@Setter
@Builder
@ToString
@NoArgsConstructor
@AllArgsConstructor
public class Report implements Serializable {
    private static final long serialVersionUID = 5966853281756014048L;
    private String startDate;
    private String endDate;
    private String dimension;
    private String measure;
    private int serviceCode;
}

Builder Pattern을 적용안했을 경우
Report report = new Report();
report.setStartDate("20170605");
report.setEndDate("201706012");
report.setDimension("age");
report.setMeasure("average");
report.setServiceCode(1);

Builder Pattern을 적용했을 경우
Report report = Report.builder().startDate("20170605").endDate("201706012").dimension("age").measure("average").serviceCode(1).build();
```

#### 7. Spring에서의 활용 방법들
- Spring Injection은 Constructor Injection을 권한다. 이유는 [Spring에서 Field Injection보다 Constructor Injection이 권장되는 이유. 참조](https://goo.gl/YsE4RY)
- 참고로 개발 편이성은 좋아질 수 있으나, 의존관계의 복잡성을 명확하게 보여주진 못하게 된다. @RequiredArgsConstructor는 초기화 되지 않은 final 필드를 매개 변수로 취하는 생성자를 생성하고 @NonNull이 필드는 null 체크가 실행되고 파라미터가 null인 경우는 NullPointerException을 발생시킨다.
  - Spring 4.3 이상인 경우
  ```
  @RequiredArgsConstructor
  @Component
  public class ConstructorInjection {
      @NonNull
      private final LoginService loginService;
      @NonNull
      private final SignupService signupService;
  }
  ```
  - Spring 4.3 이전인 경우
  ```
  @RequiredArgsConstructor(onConstructor = @__(@Autowired))
  @Component
  public class ConstructorInjection {
      @NonNull
      private final LoginService loginService;
      @NonNull
      private final SignupService signupService;
  }
  ```

#### 8. 기타
- 정리중....
- AOP를 통해 종적 관심사와 횡적 관심사의 분리.
- 높은 응집도, 낮은 결함도
- Long Method : 중복 가능성, 모듈화 낮고, 복잡성 높고, 단일 책임 원칙 위배 가능성이 있다.
