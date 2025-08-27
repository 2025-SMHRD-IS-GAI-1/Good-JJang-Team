# Hello Board Game (팀 이름:코드 유기견 보호소)
> 자바(JAVA)와 Oracle DB를 활용하여 제작한 팀 대전 기반 보드게임입니다.
> 플레이어는 회원가입/로그인을 통해 입장하며, 주사위를 굴려 이동하고 도착한 칸에 따라
> 4가지 미니게임(퀴즈,홀짝게임, Up&Down, 넌센스 퀴즈)을 플레이합니다.
> 각 팀은 보드판을 먼저 완주하는 것을 목표로 합니다.


</br>

## 0. 프로젝트 소개
- 이 프로젝트는 미니게임 외 다양한 랜덤이벤트로 지루하지 않는 플레이를 줄길 수 있습니다.  
- 게임을 개발하게 된 이유는 보드게임은 준비와 진행 과정이 번거로운 경우가 많습니다.
  이 게임은 다양한 미니게임과 자동 주사위, 말판 이동 기능을 통해 보다 간편하고 재미있게 즐길 수 있도록 제작했습니다.

## 1. 제작 기간 & 참여 인원
- 2025년 8월 22일 ~ 8월 27일
- 팀 프로젝트

1. 팀장 : 최현선(ProjectMeneger)+디자인

2. 팀원 : 김태현(오류제어담당)+디자인

3. 팀원 : 조성은(문서담당)+디자인

4. 팀원 : 송영철(설계담당)+디자인

</br>


## 2. 주요 기능
- 회원 시스템 : 회원가입 / 로그인 , 사용자 정보(DB 저장 및 관리)
- 게임 진행 : 주사위 굴리기, 팀 대전 방식, 턴 기반 진행
- 미니 게임 : 수도 맞추기, 홀짝 게임, 업다운 게임, 넌센스 퀴즈
  
### 2.1 아키텍처(MVC)
- Model : DAO, DTO를 통한 DB 연동 및 데이터 관리
- View : 콘솔 기반 UI출력
- Controller : 게임 로직과 뷰,모델 연결

## 3. 사용 기술
#### `Back-end`
  - Language : Java 11
  - IDE : Eclipse
  - Database : Query XE
  - Version Control : Git / GitHub


</br>

## 4. ERD 설계
<img width="5258" height="3612" alt="Blank diagram - Page 1" src="https://github.com/user-attachments/assets/f4521cf5-8189-42ef-ae22-504b783cb82c" />
<img width="1556" height="785" alt="image" src="https://github.com/user-attachments/assets/4bd82230-ad74-49f8-bc45-41d0d1f7fd50" />



## 5. 핵심 기능
- 이 서비스의 핵심 기능은 회원관리 + 팀 대전 기반 랜덤 이벤트 보드게임 입니다.
- 사용자는 로그인(회원등록)후 게임을 즐기기만 하면 됩니다 
- 흐름도를 보면 게임이 어떻게 진행이되고 점수가 어떻게 오르는지 알 수 있습니다!  


### 4.1. 전체 흐름
![]<img width="804" height="557" alt="image" src="https://github.com/user-attachments/assets/723e2b09-a94a-4771-a787-6b3fe961b696" />






</div>
</details>

</br>

## 7. 핵심 트러블 슈팅
### 7.1. 컨텐츠 필터와 페이징 처리 문제
- 저는 이 서비스가 페이스북이나 인스타그램 처럼 가볍게, 자주 사용되길 바라는 마음으로 개발했습니다.  
때문에 페이징 처리도 무한 스크롤을 적용했습니다.

- 하지만 [무한스크롤, 페이징 혹은 “더보기” 버튼? 어떤 걸 써야할까](https://cyberx.tistory.com/82) 라는 글을 읽고 무한 스크롤의 단점들을 알게 되었고,  
다양한 기준(카테고리, 사용자, 등록일, 인기도)의 게시물 필터 기능을 넣어서 이를 보완하고자 했습니다.

- 그런데 게시물이 필터링 된 상태에서 무한 스크롤이 동작하면,  
필터링 된 게시물들만 DB에 요청해야 하기 때문에 아래의 **기존 코드** 처럼 각 필터별로 다른 Query를 날려야 했습니다.

<details>
<summary><b>기존 코드</b></summary>
<div markdown="1">

~~~java
/**
 * 게시물 Top10 (기준: 댓글 수 + 좋아요 수)
 * @return 인기순 상위 10개 게시물
 */
public Page<PostResponseDto> listTopTen() {

    PageRequest pageRequest = PageRequest.of(0, 10, Sort.Direction.DESC, "rankPoint", "likeCnt");
    return postRepository.findAll(pageRequest).map(PostResponseDto::new);
}

/**
 * 게시물 필터 (Tag Name)
 * @param tagName 게시물 박스에서 클릭한 태그 이름
 * @param pageable 페이징 처리를 위한 객체
 * @return 해당 태그가 포함된 게시물 목록
 */
public Page<PostResponseDto> listFilteredByTagName(String tagName, Pageable pageable) {

    return postRepository.findAllByTagName(tagName, pageable).map(PostResponseDto::new);
}

// ... 게시물 필터 (Member) 생략 

/**
 * 게시물 필터 (Date)
 * @param createdDate 게시물 박스에서 클릭한 날짜
 * @return 해당 날짜에 등록된 게시물 목록
 */
public List<PostResponseDto> listFilteredByDate(String createdDate) {

    // 등록일 00시부터 24시까지
    LocalDateTime start = LocalDateTime.of(LocalDate.parse(createdDate), LocalTime.MIN);
    LocalDateTime end = LocalDateTime.of(LocalDate.parse(createdDate), LocalTime.MAX);

    return postRepository
                    .findAllByCreatedAtBetween(start, end)
                    .stream()
                    .map(PostResponseDto::new)
                    .collect(Collectors.toList());
    }
~~~

</div>
</details>

- 이 때 카테고리(tag)로 게시물을 필터링 하는 경우,  
각 게시물은 최대 3개까지의 카테고리(tag)를 가질 수 있어 해당 카테고리를 포함하는 모든 게시물을 질의해야 했기 때문에  
- 아래 **개선된 코드**와 같이 QueryDSL을 사용하여 다소 복잡한 Query를 작성하면서도 페이징 처리를 할 수 있었습니다.

<details>
<summary><b>개선된 코드</b></summary>
<div markdown="1">

~~~java
/**
 * 게시물 필터 (Tag Name)
 */
@Override
public Page<Post> findAllByTagName(String tagName, Pageable pageable) {

    QueryResults<Post> results = queryFactory
            .selectFrom(post)
            .innerJoin(postTag)
                .on(post.idx.eq(postTag.post.idx))
            .innerJoin(tag)
                .on(tag.idx.eq(postTag.tag.idx))
            .where(tag.name.eq(tagName))
            .orderBy(post.idx.desc())
                .limit(pageable.getPageSize())
                .offset(pageable.getOffset())
            .fetchResults();

    return new PageImpl<>(results.getResults(), pageable, results.getTotal());
}
~~~

</div>
</details>

</br>

## 8. 그 외 트러블 슈팅

<details>
<summary>DB 테이블 존재하지 않은 문제</summary>
<div markdown="1">

- 해결 방법 : FROM 테이블주소가  잘못되어 수정  

</div>
</details>

<details>
<summary>DB 접속 계정 오류</summary>
<div markdown="1">

- 해결 방법  :  DB 접속 계정 정보가 맞지 않아 포트 값 수정
- String url = "jdbc:oracle:thin:@project-db-cgi.smhrd.com:1524:xe";

</div>
</details>

<details>
<summary>DB 에 있는 point 데이터 추출 오류</summary>
<div markdown="1">

- 해결 방법 : DB가 들어있는 DAO에서 꺼내지 않고 , MODEL에서 빼와서 데이터가 옮겨지지 않아 DAO에서 빼냄

</div>
</details>

<details>
<summary>ASCII 스타일 명령 프롬프트</summary>
<div markdown="1">

- 해결 방법 : ascii 스타일 명령프롬포트 호환성 문제 확인 후 
- 자바 표준 출력 스트림을 UTF-8로 래핑 system.setOut(new PrintStream(System.out, true, StandardCharsets.UTF_8));

</div>
</details>

<details>
<summary>ID 중복 로그인</summary>
<div markdown="1">

- 해결 방법 : 등록할때 입력한 닉네임이 팀에 존재하면 그 이후에 유저 등록 절차를 continue로 반복문을 중간에 끊어서 처음부터 다시 유저 등록 메뉴가 뜨게 만듬

</div>
</details>

<details>
<summary>플레이어 말 겹침 충돌</summary>
<div markdown="1">

-  해결 방법 : 예외사항을 처리할 코드작성하지 않아 try/catch문을 이용하여 예외사항 처리해줌

</div>
</details>


    
</br>

## 6. 회고 / 느낀점
>프로젝트 개발 회고 글: https://zuminternet.github.io/ZUM-Pilot-integer/
