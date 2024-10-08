# 밤양갱 (Bamyanggang)
> WebRTC를 활용한 화상 채팅 기반 마피아게임
> Spring Boot Rest API와 React 클라이언트를 활용한 웹 애플리케이션
<br/>

## 1. 제작기간 & 참여인원
개발기간 
- 24.04.15 ~ 24.05.10

- 팀프로젝트 (5명)
- 역할 : FE 
    - JWT Token 유효성 검증 로직을 통한 인증 처리 (기여도 80%)
    - 메인페이지 (기여도 100%)
    - 마이페이지 (기여도 70%)
    - 커뮤니티 게시판 : CRUD, Pagination, 댓글 처리 (기여도 100%)
    - 공지사항 게시판 : 무한스크롤 기능 구현 (기여도 100%)
    


<br/>

## 2. 사용기술
### **Front-end**
- React 18.3.1
- Axios
- Java Script
- HTML5
- CSS3

### **Back-end**
- Java 17
- Spring Boot 3.3.2
- Spring Security 6.2.1
- JPA
- Redis 3.0.504
- JWT 0.12.3 

### **Database**
- MySQL 8.0.36

### **Infra**
- AWS EC2
<br/>

## 3. ERD 설계
![erdfile](/images/밤양갱erd.png)
<br/>

## 4. 핵심 기능

### 4-1. 사용자 인증 및 Token 유효성 검증
- Local Storage에 JWT Access Token을 저장한 후, decoding 하여 남은 만료시간을 계산하여 token의 유효성을 검증
    - Token이 유효한 경우, useState 함수를 통해 Token 상태를 설정하고, 인증상태를 업데이트
    - Token이 만료되었거나 없는 경우 Token 상태와 인증상태를 false로 설정

<details>
<summary>구현 코드</summary>


    const accessToken = localStorage.getItem('access');
    const [myId,setMyId] = useState("");
    //인증상태
    const [isPluggedIn, setIsPluggedIn] = useState(false);
    //토큰 유효여부
    const [hasToken, setHasToken] = useState(false);

    useEffect(()=>{
            if(accessToken){

                const decodedToken = jwtDecode(accessToken);
                const expTime = decodedToken.exp;
                const curTime = Math.floor(Date.now()/1000);

                if(expTime > curTime){
                    setHasToken(true);
                    if(myId===userIdToken){
                        setIsPluggedIn(true);
                    } else{
                        setIsPluggedIn(false);
                    }
                }
                else{
                    setIsPluggedIn(false);
                    setHasToken(false);
                }
            }else{
                //로그인을 하지 않았을 때
                setIsPluggedIn(false);
            }
                            
        },[selectedCommunity])

</details>

<br/>

### 4-2. React를 통한 Component 기반의 SPA 설계
- 페이지 Reload 없이 동적인 contents 변경 가능
- 하나의 커뮤니티 상세 페이지에서 댓글 작성, 수정, 삭제 기능을 컴포넌트화 하여 구현
- 본인이 작성한 글과 댓글에만 수정 및 삭제 버튼이 나타나도록 권한에 따른 UI 렌더링 적용

<br/>

### 4-3. 무한 스크롤 기능 구현
- 사용자가 페이지를 스크롤 할 때 추가 데이터를 비동기적으로 load하여 페이지를 동적으로 표시
- 현재 스크롤 위치를 실시간으로 계산하여 페이지 끝에 가까워질 경우 데이터를 자동으로 요청

<details>
<summary>구현 코드</summary>

    useEffect(() => {
            function handleScroll() {
                const st = window.pageYOffset || document.documentElement.scrollTop;
                if (st > lastScrollTop) {
                    
                    // 스크롤 다운
                    if (!loading && hasMore && st + window.innerHeight >= document.documentElement.offsetHeight - 100) {
                        fetchData();
                    }
                }
                setLastScrollTop(st <= 0 ? 0 : st);
            }
            window.addEventListener('scroll', handleScroll);
            return () => window.removeEventListener('scroll', handleScroll);
        }, [loading, hasMore, lastScrollTop]);

        const fetchData = async () => {
            setLoading(true);
            try {
                const response = await axios.get(`/api/notice/noticelist?page=${page}`);
                const nextData = response.data.notices;
                setNotices((prevData) => [...prevData, ...nextData]);
                setPage((page) => page + 1);
                setHasMore(nextData.length > 0);
            } catch (error) {
                console.error("Error fetching data:", error);
            } finally {
                setLoading(false);
            }
        };

</details>

<br/>


## 5. 트러블 슈팅

### 5-1. 커뮤니티 이전글/다음글 이동 시 조회수 2번 증가 문제
#### 배경
- 이전글, 다음글로 이동할 때, 페이지가 리로드 되면서 조회수가 2번씩 증가됨.

#### 문제
- useEffect 훅에서 의존성 배열 설정으로 인해 발생. 
- postNo, selectedCommunity 상태 변경 시 불필요하게 여러 번 데이터 요청이 실행됨.

#### 해결방안
- useEffect에서 postNo에만 의존하도록 의존성 배열을 최적화하여 중복 API 호출을 방지.

<br/>

### 5-2. 데이터 파싱 문제
#### 배경
- axios를 통해 데이터를 받아왔을 때, 콘솔에는 정상적으로 출력되었으나 화면에는 출력되지 않는 현상이 발생함.

#### 문제
- 객체에 데이터를 주입하는 과정에서 전체 데이터 객체가 아닌 data[0]의 배열 항목을 주입해야 했음.

#### 해결방안
- 데이터를 정확히 파싱해야 했음. 데이터 저장 과정에서의 배열 처리 문제였음을 깨달음.