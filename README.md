# SafeTrip 
여행 중 발생할 수 있는 위험 요소를 에방하고
관광 정보와 안전 정보를 함께 제공하는 통합 여행 웹 서비스

##1. 프로젝트 개요
SafeTrip은 이름의 유례에 맞게 관광지, 축제, 리뷰와 같은 일반적인 여행 정보에 주변 CCTV 위치 정보를 결합하여 
사용자가 보다 *안심하고 여행할 수 있도록 돕는 웹 서비스* 입니다.
단순 정보 제공을 넘어서 검색, 지도, 위치 기반 기능 중심으로 설계를 하였습니다.

##2. 기술 스택

## Backend ##
- Java
- Spring MVC
- JDBC Template

## Frontend ##
- JSP
- JSTL
- HTML/ CSS/ JavaScript

## DataBase
- MySQL

## API & Server
- KaKao Map API
- KaKao Geocoding API
- Apache Tomcat

## 3. 시스템 구조
- MVC 패턴 기반 설계
- Controller - Service(interface/Impl) - Repository - DB 구조
- 기능 단위 도메인 분리 (관광지, 축제, 리뷰, CCTV)

## 4. 주요 기능
## 4-1 기능 미리보기

## 관광지/축제 정보
- 관광지 및 축제 정보 CRUD
- 이미지 업로드 및 상세 페이지 제공

## 통합검색
- 키워드 기반 관광지 + 축제 통합 검색
- 하나의 화면에서 결과를 구분하여 제공

## 안전 정보
- 관광지 주변 CCTV 위치 조회
- 반경 기반 CCTV 지도 시각화

## 리뷰
- 로그인 사용자 리뷰 작성
- 다중 이미지 업로드 지원 (MultipartFile) / List<MultipartFile> 을 활용한 리뷰 다중 이미지 업로드 처리 기능 구현 파일명만 DB에 저장

## 5. 트러블 슈팅(1)

1. 통합 검색 결과가 출력되지 않은 문제
**문제**
키워드 검색 시 축제 데이터는 정상적으로 조회 되있지만,
관광지 데이터는 존재함에도 검색 결과가 출력되지 않는 문제가 발생함

**원인**
MYSQL에서 'CONCAT()'함수 사용 시 
결합되는 컬럼 중 하나라도 'NULL'값이 존재하면 
전체 결과가 'NULL'이 되어 'LIKE' 조건이 정상적으로 동작하지 않음
'' SQL
CONCAT(place_name, place_address, place_description, place_category)

**해결**
1. 검색 조건
<p>placeResult size: ${fn:length(placeResult)}</p>
<p>festivalResult size: ${fn:length(festivalResult)}</p>
코드 삽입 후 View 에 값이 들어오는지 확인 
View 확인 결과 관광지 데이터는 안들어오고 축제 데이터만 들어오는걸 발견

2. IFNULL() 함수를 사용하여 NULL 값을 빈 문자열로 치환함으로써
검색 조건이 정상적으로 동작하도록 수정

CONCAT(
  IFNULL(place_name,''),
  IFNULL(place_address,''),
  IFNULL(place_description,''),
  IFNULL(place_category,'')
)

**배운점**
SQL 함수 사용 시 NULL 처리의 중요성과 검색 로직에서 데이터 특성을 고려한 설계의 필요성을 이해함

## 5. 트러블 슈팅(2)
2. 리뷰 다중 이미지 업로드 처리 문제
**문제**
리뷰 작성 시 여러 장의 이미지를 함께 업로드 해야 했으며, 텍스트 데이터와 파일 데이터를 동시에 처리해야 하는 구조가 필요했음
**원인**
HTTP 요청 하나에 리뷰 정보(텍스트), 이미지 파일을 함께 처리해야 했기 때문에 @RequestParam 방식으로만 하기에는 구현이 어려웠음
**해결**
MultipartFile과 List<MultiPartFile>을 사용하여 다중 파일 업로드를 처리하고, 리뷰 데이터는 DB에 저장 후 생성된 PK를 이용해
이미지 파일 정보를 별도 테이블로 저장함

**배운점**
파일 업로드의 처리 흐름과
DB와 파일 시스템의 역활 분리 설계의 중요성을 다시 한번 더 학습함

## 5. 트러블 슈팅(3)
3.JSP 환경에서 SVG 아이콘 사용 시 문법 오류 발생
**문제**
JSP 페이지에서 SVG 아이콘 사용 시 IDE에서 IDE에서 Undefined attribute, Invalid location of text 등 다수의 오류 경고가 발생함

**원인**
SVG 속성에서 큰 따옴표 누락
viewBox 속성 값 미지정
IDE HTML 검증 규칙이 SVG 속성을 제대로 인식하지 못함


**해결**
잘못된 SVG 문법 
<svg width="32" height="32" fill="currentColor" class=bi bi-house-fill"
viewBox=> 
<svg width="32" height="32" fill="currentColor"
     class="bi bi-house-fill"
     viewBox="0 0 16 16">
     수정 후 IDE 오류를 제거 하고 JSP 페이지에서 정상적으로 렌더링 됨을 확인함

**배운점**
SVG는 Markdwon에서 HTML로 해석될 수 있으므로 코드 설명 시 text 블록으로 작성을 해야함
IDE 경고와 실제 실행 오류를 구분하는 디버깅 관점을 학습함


