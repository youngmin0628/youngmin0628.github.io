---
title:  "Spring Framework(3) - 프로젝트 생성 및 서버 구동"

categories:
  - Spring Framework

toc: true
toc_sticky: true
 
date: 2021-12-29
last_modified_at: 2021-12-29
---

<h1>프로젝트 생성 및 서버 구동</h1>


<h2>1. 프로젝트 생성</h2>

![image](https://user-images.githubusercontent.com/56810348/147632161-fdb9582f-44de-4e63-a5fe-4de402e84efd.png)

- 좌측 상단의 File > New > Spring Legacy Project 클릭


![image](https://user-images.githubusercontent.com/56810348/147632234-a826d306-3ca2-47fa-be68-13ebc7fceb36.png)

- Project name을 입력
- Spring MVC Project를 선택
- Next 버튼 클릭


![image](https://user-images.githubusercontent.com/56810348/147632368-12740cd3-1aab-42b9-a683-6a76e493b241.png)

- @@@@@.@@@@@.@@@@@ 식으로 입력해준다.


![image](https://user-images.githubusercontent.com/56810348/147632725-838f1a65-bb4a-4b9f-8e9c-f925712749ee.png)

- 프로젝트가 생성되었다.


![image](https://user-images.githubusercontent.com/56810348/147632886-69df7274-0ebe-4101-844a-3729c024b1b0.png)

-좌측 상단 Project 메뉴에 Properties 클릭


![image](https://user-images.githubusercontent.com/56810348/147632952-1cdf5dde-5350-4dec-9a0c-75576d064e64.png)

- Java Build Path 에 Libraries 에 JRE System Library에 버전을 맞춰주기위해 Edit 클릭


![image](https://user-images.githubusercontent.com/56810348/147633131-97609949-67e3-4a58-8155-82de31533327.png)

- JDK 11 버전을 쓰기때문에 JavaSe- 11로 변경


![image](https://user-images.githubusercontent.com/56810348/147633323-8f073987-1693-420f-8945-ab0a13568bd7.png)

- Project Facets 에 java 버전도 동일하게 변경


<h2>2. 톰캣 구동</h2>

[tomcat 9 다운로드](https://tomcat.apache.org/download-90.cgi)

![image](https://user-images.githubusercontent.com/56810348/147637209-b067d2e9-adec-4e31-9124-ce75d24e39b9.png)

- 편하게 Installer 설치


![image](https://user-images.githubusercontent.com/56810348/147637238-a97eebb0-9bd7-4bbf-b47b-74cfa58c4496.png)


- 그대로 Next 쭈욱~ 하고 마지막 Install 버튼을 클릭하면 설치가 완료된다.


![image](https://user-images.githubusercontent.com/56810348/147637617-e62453ba-d08c-451b-8731-190cc0106e3a.png)

- C:\Program Files\Apache Software Foundation 에 잘 설치가 되어있다.


![image](https://user-images.githubusercontent.com/56810348/147634703-aaa2e329-69f4-4909-b470-58d2c62dd569.png)

- 다시 Eclipse로 돌아와서 좌측 하단에 Servers에서 우클릭 > new > Server 클릭


![image](https://user-images.githubusercontent.com/56810348/147634876-75d22a2c-6b40-495f-bdb5-61e89cad4389.png)

- tomcat을 검색


![image](https://user-images.githubusercontent.com/56810348/147637699-13ae906d-4bb9-4235-9be9-293782995027.png)

- Tomcat v9.0 Server를 선택 후 Next 클릭


![image](https://user-images.githubusercontent.com/56810348/147637891-76198adf-1711-4084-875f-5e228226164f.png)

- Add 버튼 클릭


![image](https://user-images.githubusercontent.com/56810348/147637964-783fb9a6-9b8e-476c-a8dd-55becd2745c3.png)

- 옆쪽으로 옮겨졌으면 Finish 버튼 클릭


![image](https://user-images.githubusercontent.com/56810348/147638075-c2f34c8e-c566-4a5c-a3f3-46f4b7f1dc9d.png)

- 방금 생성한 서버를 클릭 후 (Start the server) 동영상 재생버튼처럼 생긴 거 클릭


![image](https://user-images.githubusercontent.com/56810348/147638151-dd93a39d-9a4e-4579-a9e2-279bb33ed24e.png)

- 이러한 에러창이 나온다면 Port설정을 바꿔줘야한다.
- 아래를 따라해보자.


![image](https://user-images.githubusercontent.com/56810348/147635364-ce8ecde9-ef9e-441f-adb3-e490ae1c540f.png)

- 저 부분을 더블클릭하면 Tomcat Ports를 설정할 수 있는 부분이 나온다.


![image](https://user-images.githubusercontent.com/56810348/147635447-51c6e71d-9fa8-437c-bfea-ebebe8ee8d0b.png)

- Tomcat admin port에 - 이게 박혀있다.


![image](https://user-images.githubusercontent.com/56810348/147638265-1c4b3d01-b6f1-4e77-ad9d-5c6632212c93.png)

- Tomcat admin port 를 편하게 바꿔준다 저는 8089로 해줬다.
- 그 아래에 HTTP/1.1 도 8088로 바꿔줬다.(Port가 겹치면 서버가 실행되지않는다.)


![image](https://user-images.githubusercontent.com/56810348/147639844-fd164462-6b49-4016-afbd-9705602610ee.png)

- 하단 탭 Modules 클릭


![image](https://user-images.githubusercontent.com/56810348/147639918-fbdd6d3d-4b81-4964-b09a-fe66cfbca715.png)

- Web Module 클릭 후 Edit 클릭


![image](https://user-images.githubusercontent.com/56810348/147640066-387da0a3-1abf-429e-bc8d-3cc82ee946c6.png)

- Path / 만 남기고 지우기
- 저장 후 서버 Start


![image](https://user-images.githubusercontent.com/56810348/147640168-2ec42a2d-ab29-4a7b-bf2e-5b9d063170e2.png)

 - 크롬열어서 localhost:8088 접속하면 서버 구동 성공
 - 위에서 서버 HTTP/1.1 port 설정할때 포트번호로 써야함
 - 8080을 그대로 뒀다면 ex) localhost:8080