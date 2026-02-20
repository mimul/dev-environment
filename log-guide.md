이 문서는 로그에 대한 운영 효율을 높일 수 있도록 로그에 대한 가이드라인을 제공하는 것을 목적으로 한다. 대상범위는 어플리케이션이 출력하는 로그(앱 로그)이며 주로 백엔드 영역의 로그를 다룬다.

## 로그 키 명명 규칙

### 언어별 로그 키 정의

각 언어별로 구조화된 로깅 라이브러리를 활용하는 경우의 키 명칭을 정리한다.

|#|Java (Logback + logstash-encoder)|Python (표준 `logging`+ python-json-logger)|
|---|---|---|
|타임스탬프|`@timestamp`|`timestamp`|
|메시지|`message`|`message`|
|로그 레벨|`level`|`levelname`|
|추적 키|`trace_id`|`trace_id`|
|파일 이름|`caller_file_name`|`filename`|
|행 번호|`caller_line_number`|`lineno`|
|클래스 이름|`caller_class_name`|해당 없음|
|스레드 이름|`thread_name`|`threadName`|
|로거 이름|`logger_name`|`name`|
|스택 추적|`stack_trace ※1`|`exc_info`/`exc_text`|

- ※1: `logger.error("DB unique 오류.", e);`와 같이 **예외 오브젝트** 를 건네주었을 때에 추가했을 경우의 키명

### Apache와 nginx

Apache와 nginx 액세스 로그에서 자주 사용되는 표준 키 이름을 정리한다.

|분류|품목|apache|nginx|
|---|---|---|---|
|Combined Log Format|클라이언트 IP 주소|remote_host|remote_addr|
||인증 사용자|remote_user|remote_user|
||타임스탬프|time|time_local|
||요청 ※1|request|request|
||HTTP 상태 코드|status|status|
||응답 크기|bytes_sent|body_bytes_sent|
||레퍼러|referer|http_referer|
||사용자 에이전트|user_agent|http_user_agent|
||요청 처리 시간|(추가 설정 필요)|request_time|
||프록시 대상 IP(XFF)|x_forwarded_for|http_x_forwarded_for|
|맞춤 항목|HTTP 메소드|method|request_method|
||요청 URI|request_uri|request_uri|

※1. request는 "GET /index.html HTTP/1.1" 형식으로 HTTP 메소드와 URI를 포함한다. 분석을 용이하게 하기 위해 커스텀 속성으로 분해할 수도 있다.

## 출력 항목

### 로그 레벨

주요 로그 레벨은 아래 표와 같다. [RFC 5424 - The Syslog Protocol](https://datatracker.ietf.org/doc/html/rfc5424) 의 Severity 정의와 [Android의 로깅 정의](https://source.android.com/docs/core/tests/debug/understanding-logging?hl=ko) 와 일관성을 갖추어 시스템의 운영 감시와 개발 효율의 두가지를 목표로 한다.

|레벨|설명|목적|지역|개발|검증|프로덕션|
|---|---|---|---|---|---|---|
|FATAL|장애|【서비스 장애】 어플리케이션의 계속이 불가능한 에러. 서비스를 중지. 필수 환경 변수가 설정되지 않은 경우.|✅️|✅️|✅️|✅️|
|ERROR|오류|【대응 필요】 특정의 요구 처리는 실패했지만, 어플리케이션 자체는 동작을 계속할 수 있는 에러. 처리중 예기치 않은 예외 등|✅️|✅️|✅️|✅️|
|WARN|경고|【잠재적인 문제】 즉시 에러는 아니지만, 장래(N영업일 이내)에 대응이 필요한 상태.|✅️|✅️|✅️|✅️|
|INFO|정보|【동작 기록】 운영자가 시스템의 상태를 판단하기 위한 비즈니스 프로세스의 중요한 이벤트. 일괄 처리 시작 / 종료 등|✅️|✅️|✅️|✅️|
|DEBUG|디버깅|【개발자용 디버그 정보】 개발자가 버그를 수정할 때 사용하는 상세 정보. 실행된 SQL 쿼리 등|✅️|✅️|||
|TRACE|추적|【개발자용 상세 추적 정보】`DEBUG` 보다 상세한 코드의 실행 정보. 메소드 시작/종료 등|✅️||||

권장 사항은 다음과 같다.

- 어플리케이션의 개별 로직에서 `DEBUG`~ `ERROR`를 사용한다 . 각 어플리케이션에서는, `TRACE` `FATAL`( `CRITICAL`)은 사용하지 않는다.
- 프로덕션은 `INFO` 레벨까지만 출력한다.

## 보안

- 기밀 정보를 기록하지 않는다. 꼭 필요한 경우 마스킹.
    - 세션 ID, 액세스 토큰, 비밀번호, API 키, 신용카드 번호, 개인정보, 사용자가 수집에 동의하지 않은 정보
        - 실수로 출력되지 않도록 로그 출력의 프레임 워크 측에서도 마스킹이나 omits 같은 빌드를 실시한다
        - 기밀 필드를 제외한, Java로 말하는 toString() 메소드나 DTO(구조체 등)를 만들어 둔다
- 액세스 제어.
    - 로그의 무단 변조를 방지하기 위해 일단 작성되면 변경 및 삭제할 수 없는 IAM 역할 설정을 수행한다.
    - 브라우징 제어도 필요한 멤버에게만 하도록 한다
- 사용자용 로그와 개발자용 로그는 분리.
    - stacktrace나 외부 API, DB가 출력하는 에러 메시지( `SQLException: Connection refused for user 'root'@'db-host'`) 등은 공격의 힌트가 되기 때문에
    - 사용자를 위한 화면 메시지는 사용자 친화적인 내용을 표시하고 개발자를 위한 자세한 내용은 로그에 표시한다.

## 성능

- 비동기 로깅
    - Java에서는 **SLF4J + Logback** 의 조합으로 AsyncAppender를 활용한다.
    - AsyncAppender는 로그 이벤트를 일단 메모리의 대기열에 저장하고 다른 작업자 스레드가 대기열에서 이벤트를 검색하고 로그 출력한다.
    - 프로세스 실패시 로그 누락에 대비하여 JVM 종료 후크로 대기열의 로그를 플러시하는 설정을 추가한다.
    - 큐가 넘치는 때의 대응은 어플리케이션을 블로킹시킨다. 로그를 삭제하도록 설정하지 말자.
- 지연 평가 활용
    - SLF4J를 사용하고 부가 항목을 메시지에 포함하는 경우 자리 표시자 ({})를 사용하고 로그 수준이 출력 대상이 아닌 경우 인수 문자열과 같은 처리 자체를 제거한다.
        - ✅️ 좋은 예:`log.debug("Processing user data: {}", userObject);`
        - ❌️ 나쁜 예:`log.debug("Processing user data: " + userObject.toString());`
    - Python logging 모듈을 사용하는 경우 f 문자열이 아닌 % 형식의 자리 표시자를 사용하여 로그 수준이 출력 대상이 아닌 경우 인수 렌더링 처리를 방지한다.
        - ✅️ 좋은 예:`logging.debug("Processing user data: %s", user_object)`
        - ❌️ 나쁜 예:`logging.debug(f"Processing user data: {user_object}")`
    - 구조화 로그의 경우, 어플리케이션측에서 toString() 등을 호출하지 않고 로거측에 맡긴다.
        - 실제로 로그 출력되는 경우에만 직렬화를 한정할 수 있다.
- 로그 출력 분기 작성
    - slf4j 등의 경우 `if (logger.isDebugEnabled()) {...}`등과 로그 레벨에 따라 분기시킨다. 특히 로그 출력용으로 문자열 가공하는 경우에는 필수로 한다

주의점은 다음과 같다.

- for 루프에서는 모든 로그를 남기는 것은 좋지 않다. 
    - 1,000건에 1번 등, 샘플링한 로그 출력을 실시한다.
- 파일명·행 번호는 로그 출력 항목으로 하지 않는다.
    - 로그 항목으로서 「파일명」이나 「행 번호」를 취득하는 처리는 스택 트레이스로부터 취득하는 경우는 부하가 높아, 성능 저하의 원인이 될 수 있다.
    - 로컬 환경만 허용하며, 배치 환경에는 출력하지 않는다. Java면 로거 이름에서 파일 이름을 식별하는 것이 쉽기 때문.
- 애플리케이션 측에서 성능 문제가 의심되는 경우 로그 출력을 의심해 봐야 한다.
    - 특히, SQL 로그 등을 대량으로 출력하는 경우 느린 경우가 있다.
- AsyncAppender를 사용할 경우 logback이 정상 종료되게 하려면 `<shutdownHook/>`를 설정한다.

logback.xml 설정 예

```
<configuration>
  <appender name="JSON_STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder class="net.logstash.logback.encoder.LogstashEncoder" />
  </appender>
  <appender name="ASYNC_JSON_STDOUT" class="ch.qos.logback.classic.AsyncAppender">
    <includeContext>true</includeContext>  
    <includeCallerData>true</includeCallerData>
    <discardingThreshold>0</discardingThreshold>
    <queueSize>4096</queueSize>
    <neverBlock>true</neverBlock>  
 .  <maxFlushTime>60000</maxFlushTime>
    <appender-ref ref="JSON_STDOUT" />
  </appender>
  <root level="INFO">
    <appender-ref ref="ASYNC_JSON_STDOUT" />
  </root>
  <statusListener class="ch.qos.logback.core.status.NopStatusListener"/>  
  <shutdownHook />
</configuration>
```

@Async에서도 traceId 유지하는 방법

```
public class ContextDecorator implements TaskDecorator {  
   @Override  
   public Runnable decorate(Runnable runnable) {  
      ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();  
      Map<String, String> callerThreadContext = MDC.getCopyOfContextMap();  
      return () -> {  
         try {  
            if (MapUtils.isNotEmpty(callerThreadContext)) {  
               MDC.setContextMap(callerThreadContext);  
            }  
            RequestContextHolder.setRequestAttributes(attributes, true);  
            runnable.run();  
         } finally {  
            RequestContextHolder.resetRequestAttributes();  
         }  
      };  
   }  
}
```
