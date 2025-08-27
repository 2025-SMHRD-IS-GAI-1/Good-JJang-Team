# Hello Board Game (팀 이름:코드 유기견 보호소)
> 자바(JAVA)와 Oracle DB를 활용하여 제작한 팀 대전 기반 보드게임입니다.
> 플레이어는 회원가입/로그인을 통해 입장하며, 주사위를 굴려 이동하고 도착한 칸에 따라
> 4가지 미니게임(퀴즈,홀짝게임, Up&Down, 넌센스 퀴즈)을 플레이합니다.
> 각 팀은 보드판을 먼저 완주하는 것을 목표로 합니다.


</br>

## 1. 프로젝트 소개
- 이 프로젝트는 미니게임 외 다양한 랜덤이벤트로 지루하지 않는 플레이를 줄길 수 있습니다.  
- 게임을 개발하게 된 이유는 보드게임은 준비와 진행 과정이 번거로운 경우가 많습니다.
  이 게임은 다양한 미니게임과 자동 주사위, 말판 이동 기능을 통해 보다 간편하고 재미있게 즐길 수 있도록 제작했습니다.

## 2. 제작 기간 & 참여 인원
- 2025년 8월 22일 ~ 8월 27일
- 팀 프로젝트

1. 팀장 : 최현선(ProjectMeneger)+디자인

2. 팀원 : 김태현(오류제어담당)+디자인

3. 팀원 : 조성은(문서담당)+디자인

4. 팀원 : 송영철(설계담당)+디자인

</br>


## 3. 주요 기능
- 회원 시스템 : 회원가입 / 로그인 , 사용자 정보(DB 저장 및 관리)
- 게임 진행 : 주사위 굴리기, 팀 대전 방식, 턴 기반 진행
- 미니 게임 : 수도 맞추기, 홀짝 게임, 업다운 게임, 넌센스 퀴즈
  
### 3.1 아키텍처(MVC)
- Model : DAO, DTO를 통한 DB 연동 및 데이터 관리
- View : 콘솔 기반 UI출력
- Controller : 게임 로직과 뷰,모델 연결

## 4. 사용 기술
#### `Back-end`
  - Language : Java 11
  - IDE : Eclipse
  - Database : Query XE
  - Version Control : Git / GitHub


</br>

## 5. ERD 설계
<img width="1556" height="785" alt="image" src="https://github.com/user-attachments/assets/4bd82230-ad74-49f8-bc45-41d0d1f7fd50" />



## 6. 핵심 기능
- 이 서비스의 핵심 기능은 회원관리 + 팀 대전 기반 랜덤 이벤트 보드게임 입니다.
- 사용자는 로그인(회원등록)후 게임을 즐기기만 하면 됩니다 
- 흐름도를 보면 게임이 어떻게 진행이되고 점수가 어떻게 오르는지 알 수 있습니다!  


### 6.1. 전체 흐름
<img width="804" height="557" alt="image" src="https://github.com/user-attachments/assets/723e2b09-a94a-4771-a787-6b3fe961b696" />



</br>

</br>

## 7. 그 외 트러블 슈팅

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

-  해결 방법 : 플레이어가 같은선상에서 출발할경우 움직이는 플레이어의 이차원 배열값을 공백으로 처리하고 자리를 떠난 플레이어의 공백자리까지 매꿔줘야했다. 조건문 추가 출발시 혹은 말이 도착할때 겹치는 경우 해당경우에는 말을 겹쳐보일 수 있게 조건문을 이용해서 해결

</div>
</details>

<details>
<summary>랜덤이벤트 발판 밟을시 연속으로 이벤트가 발동 할 수 있게 만들기</summary>
<div markdown="1">

-  해결 방법 : 해당 플레이어의 움직이는 위치값을 추출하고 해당 추출값이 플레이어가 움직여질수있는 이벤트 발판위로 갈 경우 또 실행할 수 있게 반복문안에 조건문을 배치해 이벤트 발판일경우 한번 더 반복 할 수 있게 만듦. 일반 발판일 경우 빠져나오게 만들었다.

</div>
</details>


    
</br>

## 8. 회고 / 느낀점
>프로젝트 개발 회고 글: https://zuminternet.github.io/ZUM-Pilot-integer/
