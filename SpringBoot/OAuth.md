# OAuth란?
- 제 3의 서비스에게 계정 관리를 맡기는 방식. 흔히 말하는 구글 로그인, 네이버 로그인이 있다.
## OAuth 용어 정리
  - `리소스오너(resource owner)` : 인증 서버에 자신의 정보를 사용하도록 허가하는 주체. 
    서비스를 이용하는 사용자가 리소스 오너에 해당정됨.
  - `리소스 서버(resource server)`: 리소스 오너의 정보를 가지며, 리소스 오너의 정보를 보호하는 주체
     네이버, 구글 , 페이스북 등이 해당됨
  - `인증 서버(authorization server)`: 클라이언트에게 리소스 오너의 정보에 접근 할수 있는 토큰을 발급하는 역할을 하는 애플리케이션
  - `클라이언트 애플리케이션(client application)`: 인증 서버에게 인증을 받고 리소스 오너의 리소스를 사용하는 주체. 
    지금 만들고 있는 서비스가 이에 해당함.
## 리소스 오너 정보를 취득하는 4가지 방법
- ☆☆☆ `권한 부여 코드 승인 타입(authorization code grant type)`: OAuth 2.0에서 가장 잘 알려진 인증방법.
  클라이언트가 리소스에 접근하는데 사용하며 권한에 접근할 수 있는 코드와 리소스 오너에대한 엑세스 토큰을 발급받는 형식
- `암시적 승인타입(implict grant type)`: 서버가 없는 자바스크립트 웹 애플리케이션 클라이언트에서 주로 사용하는 방법
  클라이언트가 요청을 보내면 리소스 오너의 인증 과정외에는 퀀한 코드 교환등의 별다른 인증 과정을 거치지 않고 엑세스 토큰을 제공받는 방식.
- `리소스 소유자 암호 자격증명 승인 타입(resource owner password credentials)`: 클ㄹ이언트의 패스워드를 이용해서 엑세스 토큰에 대한 사용자의 자격증명을 교환하는 방식
- `클라이언트 자격증명 승인 타입(client credentials grant)`: 클라이언트가 컨텍스트 외부에서 엑세스 토큰을 얻어 특정 리소스에 접근을 요청할때 사용하는 방식.

## 권한 부여코드 승인 타입이란?
### 권한 요청이란?
- 클라이언트 즉, 스프링 부트 서버가 특정 사용자 데이터에 접근하기 위해 권한 서버, 즉 카카오나 구글 권한 서버에 요청을 보내는ㄳ.
- 요청 URL은 권한 서버마다 다르지만 보통은 ,클라이언트 ID, 리다이렉트 URI,응답 타입등을 파라미터로 보냄
```java


```
#### 권한 요청을 위한 주요 파라미터
- `client_id`:인증서버가 클라이언트에 할당한 고유 식별자. 이 값은 클라이언트 애플리케니션을 OAuth서비스에 등록할때 서비스에서 생성하는 값
- `redirect_uri`: 로그인 성공시 이동해야하는 URI
- `response_type`: 클라이언트가 제공받길 원하는 응답 타입, 인증 코드를 받을 때는 code값을 포함해야함.
- `scope`: 제공 받고자하는 리소스 오너의 정보목록

### 데이터 접근용 권한 부여
- 인증 서버에 요청을 처음 보내는 경우 사용자에게 보이는 페이지를 로그인 페이지로 변경하고 사용자의 데이터에 접근 동의를 얻음
- 이 과정은 최초 1회만 진행되며 이 후에는 인증서버에서 동의 내용을 저장하고 있기 때문에 로그인만 진행
- 로그인이 성공되면 권한 부여 서버는 데이터에 접근 할수 있게 인증및 권한 부여를 수신함.

### 인증 코드 제공
- 사용자가 로그인에 성공하면 권한 요청시, 파라미터로 보낸 redirect_uri로 리다이렉션 됨.
- 이때 파라미터에 인증코드를 함께 제공함.
### 액세스 토큰 응답이란?
- 인증 코드를 받으면 엑세스 토큰과 교환
- 엑세스 토큰은 로그인 세션엗 대한 보안 자격 증명을 식별하는 코드로 보통 다음과 같이 POST 요청을 보냄
```json
Post /token
{
    "client-id":"jfiodjf",
    "client-secret":"fjojdiof",
    "redirect_uri":"http://localhost:8080/myapp",
    "grant-type":"authorization_code",
    "code":"fjojdof"
}
```
- `client_sceret`: OAuth 서비스에 등록할때 제공받는 비밀키
- `grant_type`: 권한 유형을 확인하는지 사용. 이때는 authorization_code로 설정해야함.
  권한 서버는 요청값을 기반으로 유효한 정보인지 확인하고 유효한 정보하면 엑세스 토큰으로 응답.

### 엑세스 토큰으로 API응답&반환
- 이때 제공 받은 엑세스 토큰으로 리소스 오너의 정보를 가져올수 있음
- 정보가 필요할때마나 API 호출을 통해 정보를 가져오고 리소스 서버는 토큰이 유효한지 검사한 뒤에 응답