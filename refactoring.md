리팩토링에 필요한 것들.
======================
리팩토링은 코드의 가독성 증대, 버그 감소, 확장성 고려 뿐만 아니라, 성능에도 영향을 줄 수 있는 중요한 행위입니다.

#### 리팩토링의 기준점들
여기서는 리팩토링을 해야하는 기준점들을 나열합니다.

- 리팩토링을 하는데에는 목표가 분명하게 있어야 한다. 그 목표는 정량적으로 산정할 수 있어야 하고, 비즈니스와 고객에 도움에 되는지가 먼저이다.
- 정량적인 목표를 먼저 생각하고 리팩토링을 해야지, 리팩토링을 하고 난 다음 정량적인 목표를 연결하려는 생각은 될 수 있으면 하지 않아야 한다. 그렇게 하면 리팩토링의 효과와 더불어 회사의 목표와 개인의 목표가 와전될 수 있다.
- 정량적인 지표들. 성능 향성, 고객 유입율 향상, 이탈율 감소 등.
- 오픈 소스의 도입을 통한 리팩토링의 경우
- 문제 해결
- AOP를 통해 종적 관심사와 횡적 관심사의 분리.
- 높은 응집도, 낮은 결함도
- 중복 최소화(Don't Repeat Yourself)
- 개방 폐쇄 원칙(Open-Closed Principal) : 소프트웨어 구성요소들(클래스, 함수, 모듈)은 확장은 가능하게 수정은 불가능하게.

#### 리팩토링을 해야할 시점
> 유사한 처리를 3 회 쓰게되면 리팩토링 <- 중복 최소화(DRY)
> 기능을 추가할 때
> 버그를 수정할 때
> 코드를 리뷰하고 난 다음
> 마틴 파울러 "리팩토링"

#### 네이밍

- 정리중

#### 리팩토링 가이드(Java)

1. 가독성 있는 if
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

2. else문 없애기: 조건문이 중첩되어 았으면 코드 이해하기도 어렵고 스크롤 압박까지 있다면 가독성이 떨어지게되고 그러면 버그를 만들 확률을 높일 수 있다. 그래서 조기 반환 기법과 전력 패턴을 사용하면 해결할 수 있다.
다음 코드가 있다고 하자.
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

#### 기타
- 정리중....
- AOP를 통해 종적 관심사와 횡적 관심사의 분리.
- 높은 응집도, 낮은 결함도
- Long Method : 중복 가능성, 모듈화 낮고, 복잡성 높고, 단일 책임 원칙 위배 가능성이 있다.
