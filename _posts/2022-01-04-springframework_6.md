---
title:  "Spring Framework - 회사 점심 투표 웹사이트(2)"

categories:
  - Spring Framework

toc: true
toc_sticky: true
 
date: 2022-01-04
last_modified_at: 2022-01-04
---

<h1>회사 점심 투표 웹사이트(2)</h1>

<br/>
밋밋한 디자인부터 입혀보고자 합니다. <br/>
아래 링크에 들어가서 다운로드를 받아줍니다.

[Bootstrap 테마다운로드](https://startbootstrap.com/template/the-big-picture)

[![image](https://user-images.githubusercontent.com/56810348/148030564-4c5db3b4-5b0b-4363-ba28-1a6a3c4be66e.png)](https://user-images.githubusercontent.com/56810348/148030564-4c5db3b4-5b0b-4363-ba28-1a6a3c4be66e.png)

- 압축을 풀고 notepad 라는 프로그램으로 index.html을 열어준다.


[![image](https://user-images.githubusercontent.com/56810348/148031899-0b3801cd-8da0-4dd8-a2bb-44a6c4b48220.png)](https://user-images.githubusercontent.com/56810348/148031899-0b3801cd-8da0-4dd8-a2bb-44a6c4b48220.png)

- html 태그 안에를 복사해서 main.jsp에 붙여넣어준다.


[![image](https://user-images.githubusercontent.com/56810348/148032678-15c9dd79-fba4-466c-8c38-b7ee2e4266f3.png)](https://user-images.githubusercontent.com/56810348/148032678-15c9dd79-fba4-466c-8c38-b7ee2e4266f3.png)

- 나머지 폴더 3개는 resources 아래에 복사해준다.


[![image](https://user-images.githubusercontent.com/56810348/148032821-c475fa12-a485-434b-8d0c-8e552033f85a.png)](https://user-images.githubusercontent.com/56810348/148032821-c475fa12-a485-434b-8d0c-8e552033f85a.png)

- 3곳의 경로를 상대경로로 바꿔준다. (../resources 추가해주면 됩니다.)
  - ex) href="/assets/favicon.ico" -> href="../resources/assets/favicon.ico"
