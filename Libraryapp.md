# 도서관리 애플리케이션
> JPA + 배포 학습

## 학습 목표
- JPA를 활용하여 CRUD를 구현한다.
- JPA 연관관계를 이해한다.
- 완성된 프로젝트를 배포한다.

## Spring Initializr
- Spring Web
- Spring Data JPA
- MySQL Driver
- H2 Database

## 학습 내용

### 1. 스프링 빈과 스프링 컨테이너

- 스프링 서버 내부에 있는 거대한 컨테이너안에 클래스가 들어가게 된다. 이렇게 들어간 클래스를 **스프링 빈** 이라고 부른다. 클래스가 들어갈 때는 이 빈을 식별 할 수 있는 이름 및 타입과 다양한 정보들이 저장되고, 인스턴스화도 진행된다.
<br/>

> 서버를 시작하게 되면 <br/>
> 1. 컨테이너가 시작된다.
> 2. 컨테이너에 기본적으로 많은 스프링 빈이 등록된다. (ex. JdbcTemplate)
> 3. 개발자가 설정해 준 스프링 빈이 등록된다. (ex. UserController)
> 4. 이 때 필요한 의존성이 자동으로 설정된다. 

### 2. JPA
- JPA란, "객체와 관계형 DB의 테이블을 짝지어 데이터를 영구적으로 저장할 수 있도록 정해진 Java 진영의 규칙"

- JPA는 API이기 때문에, 누군가는 코드로 작성을 해줘야한다. Controller를 이용해 API대로 동작할 수 있도록 코드를 작성해야 하는데 자동으로 작성해주는 것이 Hibernate이다.

### 3. 연관관계
- JPA를 활용해 객체로 매핑하는 방법
- [블로그에 정리](https://billihazero.tistory.com/11)

### 4. 배포
- 배포를 위해선 AWS EC2 서비스를 이용하여 전용 컴퓨터를 빌린다.
- 키 페어를 이용하여 리눅스 원격 컴퓨터에 접근한다.

#### 4-1. 배포를 위한 프로그램 설치
- 리눅스 컴퓨터에 프로그램을 설치한다.
    - 코드 가져오기 위한 git
    - 서버 구동할 Java
    - DB역할의 MySQL

### 4-2. 설치 명령어

```
sudo yum update : 현재 설치되어 있는 여러 프로그램들을 자동으로 최신화 해준다.
sudo yum install git -y
sudo yum install java-17-amazon-corretto -y

//MySQL GPG 체크오류로 개별적으로 다운 받았음.
sudo dnf install ./mysql-ysql-community-client-plugins-8.0.37-1.el9.x86_64.rpm --nogpgcheck
sudo dnf install ./mysql-community-common-8.0.37-1.el9.x86_64.rpm --nogpgcheck
sudo dnf install ./mysql-community-icu-data-files-8.0.37-1.el9.x86_64.rpm --nogpgcheck
sudo dnf install ./mysql-community-libs-8.0.37-1.el9.x86_64.rpm --nogpgcheck
sudo dnf instcommunity-client-8.0.37-1.el9.x86_64.rpm --nogpgcheck
sudo dnf install ./mall ./mysql-community-server-8.0.37-1.el9.x86_64.rpm --nogpgcheck
```

### 4-3. MySQL 접속 후 DB 설정
- MySQL에 접속 한 후, SQL문을 작성하여 DB를 설정한다.
    - 개발환경에서 쓰는 프로필, 배포환경에서 쓰는 프로필을 구분하여 사용할 수 있다.

### 4-4 . yml 파일 설정
- yml파일에서 profile을 설정한다.

### 4-5 . 최종 배포
- git clone을 통해 작성한 코드를 다운 받는다.
- gradle 명령어를 통해 빌드를 진행한다.
```
/gradlew build 
```
- 빌드된 jar 파일을 통해 서버를 실행한다.
