# 37-2nd-IOI-backend 프로젝트 소개 

프로젝트 기간 : 2022.10.03 ~ 2022.10.14

기술 스택 : javaScript, node.js, mysql, express, postman, bcrypt, jwt 협업 툴 : slack, notion, trello, github

구현 API
소셜로그인(카카오)  
메인페이지 : 클래스목록, 클래스 필터(인기순, 가격순) 정렬, 찜 추가  
클래스상세 페이지 : 클래스 상세 정보, 리뷰 목록, 리뷰 작성, 수정, 삭제, 좋아요 개수 관리  
찜목록 페이지 : 메인페이지 및 클래스 상세 페이지에서 찜하기한 제품 찜목록 리스트 보여주기  
클래스 등록 페이지 : 새로운 클래스 등록 페이지에서 클래스 등록하기 -> 메인페이지, 상세 페이지에서 확인 가능  
비디오 페이지 : 비디오 등록 및 재생 가능, 수정, 삭제  

역할 소개

위정우 :
소셜 회원가입, 리뷰 목록, 작성, 삭제

김정훈 :
클래스 상세 정보 불러오기, 찜 넣기, 삭제하기, 좋아요 개수 관리, 클래스 등록

# 정승렬 : 
메인 페이지 클래스 별 정렬, 필터 기능을 REST Full하게 적용하여 API 구현  

## READ
### | router |
정렬, 필터에 필요한 각 인자 값은 **쿼리파라미터** 를 통해 전달받아 처리

### | conroller |
**쿼리파라미터**로 들어온 인자들 (mainCategory, subCategory, sort, limit, offset)을 service 단계로 전달  
✋ 이때 각 인자들 중 null 값이 들어올 경우 null 값을 그대로 전달하고 이후 단계에서 처리하도록 구현

### | service |
각 인자별 null 값 처리는 dao에서 쿼리문을 만들면서 처리하기 때문에 service에서는 기능 구현이 없음

## | dao |
### Main Quary
<img width="221" alt="ioi 메인 쿼리문" src="https://user-images.githubusercontent.com/107356126/211469881-2dc0dbea-030f-462d-9d4c-da1b2b3f1ee1.png">
메인 조회 쿼리문 작성 후 인자값의 유무에 따라서 WHERE / ORDER BY 절을 추가하는 방향으로 구현  

### Order Quary (정렬에 필요한 인자 값 처리 함수)
<img width="287" alt="ioi 정렬관련 함수" src="https://user-images.githubusercontent.com/107356126/211472986-303231a5-40bb-475c-bf92-4413612bbd7b.png">

1. 기본 정렬 쿼리문을 좋아요수(내림차순), 클래스 이름(오름차순)으로 result 변수에 담아 Quary문 생성
2. 정렬에 관한 인자가 있을 경우 해당 인자에 따라 ORDER BY 뒤에 완성할 쿼리문 생성 후 반환
3. 정렬에 관한 인자가 없을 경우 기본 정렬 쿼리문을 반환

### Filter Quary (필터에 필요한 인자 값 처리 함수)
<img width="436" alt="ioi 필터관련 함수" src="https://user-images.githubusercontent.com/107356126/211473016-a90bb525-24a9-433b-a6cd-2b3d48a70bf5.png">

1. 기본 필터 쿼리문은 null 값으로 지정
2. 필터에 대한 조건은 메인 카테고리(categoryMain), 서브 카테고리(categorySub), 메인+서브 카테고리(categoryMain, categorySub) 3개의 조건에 따라 필터 쿼리문을 다르게 생성
3. 필터에 관한 인자가 없을 경우 null 값을 전달하여 WHERE절을 만들지 않고 조회 쿼리문 동작


## Bloker
초기 조회 쿼리문 작성 시 필터, 정렬 조건이 늘어남에 따라 필터수 * 정렬수 만큼의 함수를 새로 작성해야 하는 경우가 발생하여 이후 유지보수에 좋지 못하다는 판단이 들어 쿼리문 개선 작업을 진행

## 개선 내용
Query Builder를 통헤 유연하게 유지보수를 할 수 있도록 Query문 개선  
필터 항목 추가 : where 함수에 종류별 인자 값을 객체로 추가하여 where 조건식을 추가  
정렬 항목 추가 : orderby 함수에 종류별 인자 값을 객체로 추가하여 정렬 조건식을 추가  

## 성장 경험
API 구현 시 구현 이유 유지보수도 생각하면서 구현해야한다는 경험
만약 실제 서비스에서 처음 구현 방식대로 조회 쿼리문을 작성했더라면, 이후 제품이 늘어나거나 줄어들면 각각의 함수를 찾아서 차구 또는 삭제를 해야 했을 것
하지만 개선된 조회 쿼리문으로는 추가, 삭제된 인자에 관한 조건만 수정해주면 되기 때문에 유지보수가 수월하다는 장점이 있다.






ERD
![image](https://user-images.githubusercontent.com/109129718/195786717-0b3248ce-5794-4af5-a969-ad81f3e9e386.png)
