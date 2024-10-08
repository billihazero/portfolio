# Spring Security + JWT Study
> Spring Security + JWT 를 활용한 로그인 /로그아웃 기능 구현 학습

## 학습 목표
- 로그인 성공 시, Access/Refresh에 해당하는 다중 Token을 발급한다.
- 각각의 Token은 사용처에 따라 서로 다른 저장소에 발급하여 저장한다.
    - Access : Header에 발급후 프론트에서 저장
    - Refresh : Server 저장소에 저장한다.

## Spring Initializr
- Lombok
- Spring Web
- Spring Security
- Spring Data JPA
- MySQL Driver

## 학습 내용

### 1. 정의
- JWT란 JSON Web Token의 약자로, 서버 세션이 유저 정보를 저장하는 세션 방식과 달리, Token 생성 메소드를 통해 Token을 생성하여 응답한다.
- JWT는 유저를 인증하고 식별하기 위한 Token 기반 인증이다.
- Token 자체에 사용자의 권한 정보나 서비스를 사용하기 위한 정보가 포함된다.

### 2. 구조

JWT는 세 파트로 나누어지며, 각 파트는 점으로 구분하여 표현된다.

```
{Header}.{Payload}.{Signature}
```
- Header : JWT임을 명시하고, 사용된 암호화 알고리즘의 정보가 담겨 있다.
- Payload : 토큰에서 사용할 정보의 조각들인 클레임(claim)(토큰 발급자, 발급시간, 만료시간, 권한)이 담겨 있다. 클레임은 Key-Value 형태의 값을 가진다.
- Signature : Header에서 지정한 알고리즘과 secret key가 담겨 있다.

### 3. 구현
- JwtUtil
- JwtFilter
- SecurityConfig
- LoginFilter
- LogoutFilter
- CustomUserDetails

### 4. 동작과정

![jwt](/images/jwt.png)

### 5. 장점
- 다양한 클라이언트(Web, Android, IOS) 에서 사용 가능
- 세션 / 쿠키에 비해 높은 보안성 제공
- stateless 인증 방식으로 서버 상태 관리가 필요 없음.

### 6. 단점
- Token 크기 : 세션과 같은 간단한 토큰에 비해 크기가 큼.
- Token 무효화 어려움 : stateless 인증 방식으로 Token을 저장하지 않기 때문에 중간에 무효화하거나 강제로 로그아웃 하는 것이 어려움.
- 보안 위험 : 만료 시간 동안 탈취된 Token을 사용할 위험 가능성이 있음.

### 7. 저장

각 토큰 사용처에 따라 알맞은 저장소를 설정해야한다.

- 로컬 스토리지 : XSS 공격에 취약
- httpOnly 쿠키 : CSRF 공격에 취약

-> Refresh token을 서버 측 저장소에 저장하였다.

해당 프로젝트에서는 MySQL DB에 저장하였으나, 조회 성능 향상과 TTL 설정을 위해 Redis에 저장하는 것이 좋다는 것을 확인하였다.

+추가로 프론트에서 메모리에 저장하는 방법도 생각해 볼 수 있다.