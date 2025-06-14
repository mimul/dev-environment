이 문서는 웹 API를 사용하는 개발자를 위해 REST와 같은 웹 API의 가이드 라인을 제공한다. gRPC, JSON-RPC, SOAP, GraphQL 등 RPC나 그래프 지향이 아닌 REST 지향의 Web API 구축이 대상이다.

## 호스팅

| 항목    | 서브 도메인 방식                                                  | 서브 패스 방식                                       |
|:-------|:--------------------------------------------------------------|:--------------------------------------------------|
|예시     |api.foo.com                                                    |foo.com/api                                       |
|설명     |웹사이트와 API를 분리할 수 있고, 독립적인 API(마이크로) 서비스를 제공할 때 적합 |동일 도메인에서 정적 콘텐츠, Web API를 같이 서비스 할 때 이용 |
|DNS 관리 |필요                                                            |불필요                                             |
|CORS 설정|필요                                                            |불필요                                             |

- 동일한 도메인에서 정적 콘텐츠(HTML/CSS/JavaScript/이미지 등)를 배포해야 한다면 서브 패스 방식을 채택한다.
-  마이크로 서비스 아키텍처를 채용하거나 할 예정이라면 서브 도메인 방식을 채택한다.

## 스타일

|항목               |추천                |예시              |이유                                                                                                      |
|:-----------------|:------------------|:----------------|:--------------------------------------------------------------------------------------------------------|
|호스트 이름          |kebab-case         |api.foo.com      |호스트명에는 언더 스코어(_)의 사용을 RFC 952, RFC 1123에서는 허용하고 있지 않다.|
|경로(자원 이름)       |kebab-case + 복수형 |schedules        |호스트명이 kebab-case이기 때문에 일관성을 유지한다. 또, REST 지향이라고 하는 전제이기 때문에, 자원명의 마지막은 복수형으로 한다|
|요청 헤더            |kebab-case        |x-debug-enabled   |              |
|응답 헤더            |kebab-case         |x-debug-logs      |              |
|쿼리 매개변수         |snake_case         |order_id           |              |
|요청 본문의 JSON      |snake_case         |order_id          |              |
|응답 본문의 JSON      |snake_case         |order_id           |              |

## API 버전 관리

|항목                    | 패스 방식                       |HTTP 헤더 방식                               |서브 도메인 방식       |
|:----------------------|:------------------------------|:------------------------------------------|:--------------------|
|설명                    |URI의 일부에 버전 정보를 부여하는 방법 |요청 헤더에 버전 정보를 부여하는 방법                |도메인에서 지정하는 방법  |
|예시                    |`/v1/user/100`                 |`Accept: application/vnd/example.v1+json`  |`v1.example.com`     |
|REST와 조합              | 좋지 않음                       |좋음                                        | 좋음                 |
|openapi.yaml 스키마 관리  | 좋음                           |응답 항목이 다르면 정의하기 어려움                 | 다른 파일로 관리        |
|openapi.yaml 코드 생성   | 좋음                           | 응답 항목이 다르면 정의하기 어려움                 | 정의 파일별로 생성      |
|인프라 구성               |이슈 없음                        |이슈 없음                                     | 하위 도메인 설정 필요   |
|로그                    | 버전 번호 로그에 남기기 쉬움         | 헤더도 출력하지 않으면 버전을 알기 어려움            | 풀패스를 지정하면 됨    |

- REST 사상의 갭이 존재하지만 실용적 관점에서 패스방식을 채택한다.

## HTTP 메소드

|메소드   | 부작용      | 균등성      |설명                                                                                           |
|:------|:-----------|:----------|:---------------------------------------------------------------------------------------------|
|HEAD   | 안전        | 보장       | 헤더 획득. GET과 달리 응답 본문을 검색하지 않음                                                        |
|GET    | 안전        | 보장       | 조회                                                                                          |
|POST   | 안전하지 않음 | 보장하지 않음 |리소스 생성/업데이트/추가 비동기 요청의 경우도 이용, 신규의 경우 요청마다 자원이 추가됨                           |
|PUT    | 안전하지 않음 | 보장        |요청 바디에 의해서 자원을 작성, 신규는 POST로, 여러번 호출해도 하나의 리소스. 멱등성 보장                        |
|PATCH  | 안전하지 않음 | 모호        |지정된 항목만 부분적으로 갱신                                                                       |
|DELETE | 안전하지 않음 | 보장        |리소스 삭제                                                                                     |

- HTTP 요청 방법은 [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4.3) 및 [RFC 5789](https://datatracker.ietf.org/doc/html/rfc5789#section-2) 에 정의되어 있다.
- [RFC 7230 스펙에서 8000 문자 이상의 제한](https://datatracker.ietf.org/doc/html/rfc7230#section-3.1.1)이 HTTP 구현에 요구된다.
- 조회는 기본적으로 GET 방법을 사용한다. 캐시에 효과적이고 북마크 할 수 있고 링크로 공유할 수 있어 편리성이 있다.
- GET에서 요청 본문(콘텐츠)에 매개변수를 지정하는 방법은 채택하지 않는다. HTTP 소프트웨어에 의해 무시, 거부될 수 있다.
- 안전은 리소스의 상태를 변화시키지 않는 읽기 전용인 것이다.
- 균등성은 동일한 파라미터로 1회 이상 요청을 해도 리소스의 상태가 같은 것이다.

## HTTP 상태 코드

|상태 코드                 |설명                                                                          |GET/HEAD|POST|PUT|PATCH|DELETE|
|:-----------------------|:----------------------------------------------------------------------------|:-------|:---|:---|:---|:---|
|200 OK                  |검색 성공, 업데이트 성공                                                           |사용     |    |    |    |    |
|201 Created             |등록 처리로 정상 종료(동기)                                                        |         |사용 |일시 |    |    |
|202 Accepted            |비동기 처리 호출로 정상 종료                                                        |         |사용 |    |    |    |
|204 No Content          |정상 종료하고 비어있는 응답                                                         |         |     |사용 |사용 |사용 |
|400 Bad Request         |입력값 유효성 검증 오류                                                            |사용      |사용   |사용 |사용 |사용 |
|401 Unauthorized        |인증 실패                                                                       |사용      |사용   |사용 |사용 |사용 |
|403 Forbidden           |권한 오류                                                                       |사용      |사용   |사용 |사용 |사용 |
|404 Not Found           |존재하지 않는 자원 혹은 경로 파라미터 지정으로 존재하지 않는 경우                             |사용      |사용   |사용 |사용 |사용 |
|405 Method Not Allowed  |허용하지 않는 HTTP 메소드 지정                                                       |사용      |사용   |사용 |사용 |사용 |
|409 Conflict            |다른 사용자의 작업으로 리소스가 업데이트된 경우, 고유 제한조건 위반, 낙관/비관 잠금 오류가 발생한 경우 |         |사용   |사용 |사용 |사용 |
|412 Precondition Failed |If-Match로 낙관 잠금을 이용한 갱신을 실시하는 경우는 409가 아니라 412를 이용                 |         |      |     |   |    |
|413 Payload Too Large   |요청 헤더, 요청 본문이 조건을 초과하는 경우                                              |         |사용   |사용 |사용 |    |
|414 URI Too Long        |요청된 URL 길이가 규정보다 긴 경우                                                    |일시      |      |    |    |    |
|422 Unprocessable Entity|입력 값이 업무 처리를 수행하는 조건을 충족시키지 못함                                       |사용      |사용   |사용 |사용 |사용 |
|429 Too Many Requests   |속도 제한 초과                                                                     |사용      |사용   |사용 |사용 |사용 |
|500 내부 서버 오류         |시스템 오류(DB 연결 오류, SQL 실행 오류, 외부 웹 API 중 런타임 오류 등)                      |사용      |사용   |사용 |사용 |사용 |
|501 Not Implemented     |개발 단계에서만 허용, 상용에서는 릴리스를 해서는 안됨                                        |일시      |일시   |일시 |일시 |일시 |
|503 Service Unavailable |Web API 서버의 전단의 Load Balancer 등에서 503을 돌려줄 수 있음                          |사용      |사용   |사용 |사용 |사용 |

## 요청 헤더

|항목             | 추천|
|:---------------|:-----------------------------------------------------------------------------------------|
|Authorization   | 인증 토큰 등은 Authorization 헤더(또는 Cookie)로 설정함                                          |
|Content-Type    | API가 지원하는 미디어 유형을 지정. `application/json`                                           |
|User-Agent      | 이용 실적의 파악을 위해 지정. 예로 `User-Agent: PcClient/1.0`                                    |
|Accept-Language | 다국어 지원 웹 API의 경우 지정 가능                                                              |

**1. 품질 헤더**

특정의 요청 헤더에서는 품질값(Quality Value / q값)에 0~1까지의 수치를 지정하는 것으로, 클라이언트가 취득하고 싶은 형식이나 인코딩의 우선도를 지정할 수 있다.

|헤더 이름         |설명                                            |q값 예                                                  | 추천              |
|:---------------|:----------------------------------------------|:------------------------------------------------------| :---------------|
|Accept          | 미디어 타입(MIME 타입)의 우선순위로 표시               |Accept: application/json;q=0.9, application/xml;q=0.8 | q 이용하지 않음     |
|Accept-Encoding | 인코딩(압축 형식 등)의 우선순위로 표시                 |Accept-Encoding: gzip;q=1.0, deflate;q=0.6             | q 이용하지 않음     |
|Accept-Language | 언어의 우선순위 표시                               |Accept-Language: ko-KR;q=0.9, en-US;q=0.9              | 다국어 대응시 사용 |
|TE              | 전송 인코딩(trailers, chunked 이외)의 우선순위로 표시 |TE: trailers, deflate;q=0.5                             | q 사용하지 않음    |
|Want-Digest     | Digest 헤더, 해시 알고리즘의 우선 순위 순서를 나타냄     |Want-Digest: sha-512;q=1.0, sha-256;q=0.8               | q 사용하지 않음    |


## 응답 헤더

**1. Content-Type**
- 항상 Content-Type 설정한다. 적절한 미디어 유형을 사용하고 오류의 경우 `application/problem+json`을 사용한다.
- 인코딩 지정은 필요하지 않다 (RFC 8259부터 JSON 인코딩은 UTF-8 전용임)

**2. Server-Timing**
- 튜닝이 필요한 API에  Server-Timing 헤더를 사용한다.
- 예시로 `Server-Timing: cache;desc="Cache Read";dur=11.4, db;dur=23, app;dur=33.5`는 cache 꺼내는데 11.4밀리초, db 쿼리에 23밀리초, app로직에 33.5밀리초가 걸린 것을 나타낸다.

**3. Cache-Control**
- [Cache-Control](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Cache-Control)을 이용하면 브라우저나 CDN이나 프록시 등에 대해서 캐시를 제어하기 위한 지시를 제어할 수 있다.
- 기본 정책으로는 응답 헤더에 `Cache-Control: no-store`추가하고 캐시를 금지한다.

## 응답 본문

**1.공통사항**

- 프런트 엔드가 취급하기 쉬운 형태로 제공한다.
- 날짜 형식은 ISO8601 형식을 사용한다.
- `null`은 사용하지 않고, 파라미터의 키 자체를 포함하지 않는다.
- 중요한 정보는 암호화(AES)한다.
- 응답값엔 항상 응답 결과에 대한 정보를 포함한다.

|항목            |설명                                          |
|:--------------|:--------------------------------------------|
|responseStatus | 응답값 상위 객체                                |
|code           | 오류 코드(하위), 100은 성공, 100이 아니면 에러상태이다 |
|message        | 오류일 경우 상세 메세지(하위)                      |

**1.목록**

```
{
  "numberOfElements": 20,
  "totalElements": 25
  "sort":,
  "totalPages": 2
  "content": [
    { "id": 1, "name": "Item 1" },
    { "id": 2, "name": "Item 2" }
  ],
  "responseStatus": {
    "code": 100,
    "message": "Success"
  }
}
```

- 페이징의 경우 totalPages(전체 페이지 수), totalElements(전체 목록수), numberOfElements(페이지당 목록 수), sort(정렬정보)가 제공된다.

## 낙관적 락
낙관적 잠금은 동일한 엔티티에 동시에 쓰기가 발생하고 데이터 무결성이 손실되는 것을 방지하는데 사용된다. DB의 각 테이블에는 버젼 번호가 존재하는 것을 전제로 한다.

|구분 |구분상세  |If-Match 헤더와 ETag 헤더| 결과 엔티티의 ETag| 버전 번호|
|:---|:-------|:---|:---|:---|
|개요 |처리 흐름         |![f-Match+ETag](https://github.com/mimul/dev-environment/blob/master/images/if_match_etag.png) | ![ETag](https://github.com/mimul/dev-environment/blob/master/images/etag.png)  | ![Version](https://github.com/mimul/dev-environment/blob/master/images/version.png)  |
|    |설명             |업데이트 전에 업데이트 행을 다시 가져오고 응답 헤더의 Etag로 버전을 가져옴         |일람 취득의 바디로부터 버젼을 취득해, If-Match 헤더에 취득한 버젼을 설정 |목록 검색 바디에서 버전을 가져오고 요청 바디에 버전 설정|
|조건 |ETAG 헤더 사용    |있음| 없음| 없음|
|    |If-Match 헤더 사용| 있음| 있음| 없음|
|성격 |REST 사상        |적합 | 부분적으로 적합|적합하지 않음|
|    |API 호출 수       | 많아짐| 적당 |적당 |
|    |벌크 기능         | 불가능| 불가능| 가능|
|    |요약             |개발 생산성에서는 불리하지만 REST 설계사상을 지키고 싶은 경우에 채용           |벌크 업데이트가 필요하지 않은 경우 REST 사상을 적당히 지키는 경우 선택|목록 검색 후 대량 업데이트가 필요한 경우 채택|

- 버전 번호를 이용해 낙관 락을 사용한다.

## 인증

**1. 인증방식**

|구분       |API 액세스 키                                                         | 클라이언트 자격 증명 흐름        | mTLS                      |
|:---------|:-------------------------------------------------------------------|:--------------------------| :-------------------------|
|설명       |x-apikey 헤더로 액세스 키를 연계하는 방식. API Gateway가 제공하는 기능의 일부를 활용|인증 서버에 클라이언트 ID, 클라이언트 비밀을 전달하고 액세스 토큰을 얻는 방법|TLS 상호 인증으로 클라이언트 인증서를 사용하는 방법|
|파트너 사용성|약간 절차 복잡                                                           |약간의 절차 복잡                                              |클라이언트 인증서 로드가 필수|
|처리 성능   |구조가 단순하기 때문에 높음                                                 |토큰의 취득 및 검증에서 성능 불리함                                 |큰 페널티 없음|
|보안       |API키 누설 등 부정 이용 위험 있음                                           |액세스 토큰에 만료있어 위험을 제한                                 |클라이언트 인증서의 유출에 취약|
|Throttling|API Gateway로 액세스 수 제한이 가능                                        |개별 구현 필요                                                |개별 구현 필요|
|인프라 리소스|API 게이트웨이 증가                                                       |기존의 구조에 편승 가능                                         |상호 인증 API 게이트웨이 증가|
|설계 비용   |API 키와 액세스 토큰의 두 가지 패턴 대응 필요                                  |액세스 토큰 방식으로 통일 가능                                    |API 키와 액세스 토큰의 두 가지 패턴 대응 필요|

- 클라이언트 자격 증명 흐름 방식을 선택한다.

**2. 인증흐름**

|구분 | 베어러 토큰 방식|세션 토큰 방식|
|:-------------|:---|:---|
|설명           |API 요청마다 액세스 토큰을 Authorization 헤더에 포함하는 방식|최초 요청만 액세스 토큰을 이용하고, 이후는 세션 관리(세션 ID를 쿠키에 포함시켜 송신)에 의한 인증을 실시하는 방식.세션 관리 테이블이나 캐시 서버가 필요|
|처리 성능       |요청 시마다 토큰 인트로스펙션 API에서 확인이 필요             |서버 측 세션 관리 부분의 성능이 요구됨|
|토큰 비활성화    |IdP의 리보케이션 API로 무효화 가능                        |쉬움                          |
|재로그인 필수 기간|IdP측의 리프레시 토큰이 무효가 될때                        | 임의의 조정 가능|
|IdP와의 결합 정도| 액세스마다 IdP에 의존                                 | IdP 의존 위치가 최소화됨|
|보안           |액세스 토큰을 로컬 스토리지나 쿠키에 보관해야 함              |액세스 토큰은 Web API 인증 후에 폐기 가능하며 보유 기간을 최소화할 수 있음|
|curl과의 궁합   |비교적 용이                                          |약간의 절차가 복잡|
|로컬 개발 환경   | 쉬움                                               | 쉬움|

- 세션 토큰 방식을 활용한다.

## 권한 제어

유저의 ID와 권한을 IdP(Identity Provider)에 가져갈지, 어플리케이션 측에서 가져갈지를 판단한다.

|구분       |IdP 관리                                                  | 애플리케이션에서 관리                                             |
|:---------|:--------------------------------------------------------|:------------------------------------------------------------|
|설명       |IdP 측에서 사용자 계정과 권한을 함께 관리하는 방법                  |IdP와 애플리케이션 측에서 계정을 이중으로 유지하고 권한은 어플리케이션 측에서 연결하는 방법|
|확장성     |IdP의 권한 모델이 어플리케이션의 요구에 부합하지 않는 경우 수정 비용이 높음|IdP에 의존하지 않고 어플리케이션 내에서 일관된 역할 관리 및 액세스 제어가 가능|
|데이터 무결성|IdP 측에 가까워지면 일관성이 유지되기 쉬움                         |IdP와 앱측의 다중 관리가 되므로 무결성을 유지 고민 필요|

- 애플리케이션에서 관리하는 방법을 선택한다.

## 보안

**1. 응답 헤더**

|항목 |권장값|필수|설명|
|:---|---|---|---|
|[Content-Security-Policy](https://developer.mozilla.org/ja/docs/Web/HTTP/CSP)                                |default-src 'none';                 | 필수 |CSP. 크로스 사이트 스크립팅 (XSS)의 완화 방법|
|[X-Content-Type-Options](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/X-Content-Type-Options)      |nosniff                             | 필수 |브라우저가 MIME 유형을 자동으로 판별하는 것을 방지하고 특정 유형의 파일만 처리하도록 함으로써 콘텐츠 분석 및 주입 공격을 방지함|
|[Strict-Transport-Security](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Strict-Transport-Security)|`max-age=63072000; includeSubDomains| 필수 |[HSTS](https://hstspreload.org/) : HTTP Strict Transport Security를 ​​활성화하고 HTTPS를 강제하고 중간자 공격(MITM)을 완화함|

**2. CORS**

- Access-Control-Allow-Origin은 와일드카드를 금지한다.
- Access-Control-Allow-Headers는 실제로 사용하는 헤더만 지정한다.
- Access-Control-Allow-Methods는 실제로 사용하는 메소드만 지정한다.
- Access-Control-Allow-Credentials는 쿠키나 인증 헤더를 포함하는 경우 true로 한다.
- Access-Control-Max-Age는 기본값은 5초이다.

**3. 쿠키**

|항목     |단위              |설명                                                                    |설정 예|
|:-------|:----------------|:----------------------------------------------------------------------|:---|
|보안     |없음              |HTTPS 요청 시에만 전송됨                                                    |보안|
|HttpOnly|없음              |자바스크립트에서 쿠키에 액세스할 수 없다. XSS의 위험을 줄일 수 있음                   |HttpOnly|
|SameSite|Strict, Lax, None|쿠키 전송 제어를 위한 속성(후술)|SameSite=Lax                                 |
|Path    |경로              |송신 대상의 패스를 지정 (설정한 패스의 서브 패스에도 송신된다)                       |Path=/api|
|도메인    |도메인 이름         |대상 도메인을 지정. 생략하면 게시자의 호스트 이름으로 제한되며 하위 도메인간에 공유되지 않음|          |
|Expires  |날짜(GMT)        |만료일을 GMT 형식으로 지정                                                  |Expires=Tue, 29 Apr 2025 12:00:00 GMT|
|Max-Age  |초               |유효기간을, 현재 시각으로부터 초로 지정. Expires보다 우선. 0이하는 즉시 삭제         |Max-Age=86400 (24시간)|

`SameSite`의 설정값에 대해 기술한다.
- **Strict** : 동일한 사이트에서 요청한 경우에만 전송. 
- **Lax** : Strict의 한계를 조금 완화해 다른 사이트로부터의 전이에서도, 안전한 HTTP 메소드(GET등)에 의한 톱 레벨 네비게이션의 경우는 Cookie를 송신. POST 요청나 iframe, Ajax등에서는 송신되지 않음.
- **None** : 크로스 사이트 요청을 포함한 모든 쿠키를 보냄. CSRF 토큰 등의 대책 필요.

## 성능

**1. 페이징**

|항목 |오프셋 & 리미트 방식|페이지 번호 방식| 커서베이스 방식|
|---|---|---|---|
|개요          |요청에서 지정한 `offset`(시작 위치) 및 `limit`(취득 건수)에 따라 결과를 얻음  |요청에 지정한 것과 `page`같은 `size`매개 변수를 사용하여 페이지 단위로 데이터 검색|특정 커서 값을 기준으로 다음 데이터 세트를 얻음|
|페이지 번호 계산 |클라이언트 측에서 실시                                                |서버측에서 실시                                                      |-|
|친화력이 높은 UI |페이징                                                            |페이징                                                            |무한 스크롤|
|RDB와의 친화성  |높음                                                              | 높음                                                            | 보통|
|NoSQL과의 친화성 |낮음                                                             |낮음                                                             | 높음|
|성능           |처음부터 목표까지 모든 행을 세어야 하기 때문에 나중의 페이지가 될수록 응답이 나빠짐 |오프셋 & 리미트 방식과 동일                                           |페이징 위치와 관련된 오버헤드 없음|
|데이터 무결성    |검색할 때마다 치아나 중복이 발생할 수 있음                                 |오프셋 & 리미트 방식과 동일                                           |정렬 결과에 따라 고유하게 결정되는 값을 기준으로 하므로 중복이나 치이, 빠짐은 발생하지 않음|
|특정 페이지로 이동|쉬움                                                              |쉬움                                                             |어려움|

- 페이지 번호 방식을 선택하되 이전 페이지의 마지막 키값을 가지고 다음페이지 조회시 요청 쿼리에 지정하고 page size만큼 데이터를 볼러오는 방식으로 하면 성능 이슈가 없다.

**2. Rate Limit**

Rate Limit (레이트 제한)은 특정 시간 내에 허용되는 요청 수를 제한하는 메커니즘이다. 대량 요청으로 서버 부하를 보호하고, 공정한 자원 배분, DDoS 공격, 무단 액세스를 시도하는 봇 등으로부터 서비스 보호할 수 있다.

Rate limit의 경우는 `429 Too Many Requests`, `Retry-After`헤더를 반환한다.

**3.타임아웃**

타임아웃을 적절히 설정함으로써 자원의 낭비를 방지하거나 클라이언트의 대기 시간에 의한 스트레스를 경감할 수 있다. 애플리케이션 측에서 타임 아웃을 구현하지 않고 웹서버나 WAS에서 지정한다.

