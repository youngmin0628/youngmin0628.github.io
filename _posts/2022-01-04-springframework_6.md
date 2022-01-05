---
title:  "Spring Framework - 회사 점심 투표 웹사이트(2)"

categories:
  - Spring Framework

toc: true
toc_sticky: true
 
date: 2022-01-04
last_modified_at: 2022-01-05
---

<h1>회사 점심 투표 웹사이트(2)</h1>

<br/>

밋밋한 디자인부터 입혀보고자 함. <br/>
아래 링크에 들어가서 다운로드를 받아줌.

[Bootstrap 테마다운로드](https://startbootstrap.com/template/the-big-picture)

[![image](https://user-images.githubusercontent.com/56810348/148030564-4c5db3b4-5b0b-4363-ba28-1a6a3c4be66e.png)](https://user-images.githubusercontent.com/56810348/148030564-4c5db3b4-5b0b-4363-ba28-1a6a3c4be66e.png)

- 압축을 풀고 notepad 라는 프로그램으로 index.html을 열어줌.


[![image](https://user-images.githubusercontent.com/56810348/148031899-0b3801cd-8da0-4dd8-a2bb-44a6c4b48220.png)](https://user-images.githubusercontent.com/56810348/148031899-0b3801cd-8da0-4dd8-a2bb-44a6c4b48220.png)

- html 태그 안에를 복사해서 main.jsp에 붙여넣어줌.


[![image](https://user-images.githubusercontent.com/56810348/148032678-15c9dd79-fba4-466c-8c38-b7ee2e4266f3.png)](https://user-images.githubusercontent.com/56810348/148032678-15c9dd79-fba4-466c-8c38-b7ee2e4266f3.png)

- 나머지 폴더 3개는 resources 아래에 복사해줌.


[![image](https://user-images.githubusercontent.com/56810348/148032821-c475fa12-a485-434b-8d0c-8e552033f85a.png)](https://user-images.githubusercontent.com/56810348/148032821-c475fa12-a485-434b-8d0c-8e552033f85a.png)

- 3곳의 경로를 상대경로로 바꿔줌. (../resources 추가해주면 된다.)
  - ex) href="/assets/favicon.ico" -> href="../resources/assets/favicon.ico"


[![image](https://user-images.githubusercontent.com/56810348/148175989-48a80aca-043d-4fa0-b687-39a54911fec1.png)](https://user-images.githubusercontent.com/56810348/148175989-48a80aca-043d-4fa0-b687-39a54911fec1.png)

- Menu Controller, Service, DAO 를 추가해준 것처럼 Main 패키지도 만들어서 똑같이 추가해줬음.

**MainController.Class**
```java

package com.ym.main.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class MainController {

	@GetMapping(value="/")
	public String MainPage() throws Exception {
		return "main";
	}
	
}

```

- 나머지 Main쪽 Service, DAO는 차근차근 작성해보겠음.
- 일단 Main 화면의 디자인을 조금만 더 수정해주겠음.


[![image](https://user-images.githubusercontent.com/56810348/148178708-79a92196-3c9b-431a-8fc1-09bb5553fd19.png)](https://user-images.githubusercontent.com/56810348/148178708-79a92196-3c9b-431a-8fc1-09bb5553fd19.png)

- ctrl + shift + R 에서 style.css를 찾아서 저 부분을 ctrl + shift + / 로 주석처리를 해줌.


[![image](https://user-images.githubusercontent.com/56810348/148179987-7fe56153-457b-41ac-ae4e-6a2588d6a476.png)](https://user-images.githubusercontent.com/56810348/148179987-7fe56153-457b-41ac-ae4e-6a2588d6a476.png)

- 같은 방법으로 server.xml을 찾아서 똑같이 수정해줌.
  - 보안상의 이유로 파일을 읽을 수가 없어서 설정해줌.


[![image](https://user-images.githubusercontent.com/56810348/148180251-81c9e618-f742-4a80-80a4-356c0ac2f308.png)](https://user-images.githubusercontent.com/56810348/148180251-81c9e618-f742-4a80-80a4-356c0ac2f308.png)

- 위에 docBace에 설정해준 경로대로 C드라이브 밑에 imageFile 폴더를 만들어줌.
  - 저 폴더안에 존재하는 파일들은 이제 접근이 가능하다.


[![image](https://user-images.githubusercontent.com/56810348/148180691-19c3d0c9-e563-49c2-86dc-949a2c35821b.png)](https://user-images.githubusercontent.com/56810348/148180691-19c3d0c9-e563-49c2-86dc-949a2c35821b.png)

- 대충 돈까스 사진을 놓고 Test를 해보겠다.


[![image](https://user-images.githubusercontent.com/56810348/148180900-e540ce93-b223-43dc-812c-2cc1042f479f.png)](https://user-images.githubusercontent.com/56810348/148180900-e540ce93-b223-43dc-812c-2cc1042f479f.png)

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
	<head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
        <meta name="description" content="점심식사를 고민하는 게 싫어서 만든 프로그램" />
        <meta name="author" content="ym" />
        <title>점심식사 뽑자</title>
        <script  src="http://code.jquery.com/jquery-latest.min.js"></script>
        <!-- Favicon-->
        <link rel="icon" type="image/x-icon" href="../resources/assets/favicon.ico" />
        <!-- Core theme CSS (includes Bootstrap)-->
        <link href="../resources/css/styles.css" rel="stylesheet" />
        
        <!-- Bootstrap core JS-->
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
        <!-- Core theme JS-->
        <script src="../resources/js/scripts.js"></script>
    </head>
    	<script>
    	$( document ).ready(function() {
    	    $('body').css('background','url("/image_path/donggas.jpg") no-repeat center center fixed');
    	});
    	</script>
    <body>
        <!-- Navigation-->
        <nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-bottom">
            <div class="container px-4 px-lg-5">
                <a class="navbar-brand" href="/">Home</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarResponsive" aria-controls="navbarResponsive" aria-expanded="false" aria-label="Toggle navigation"><span class="navbar-toggler-icon"></span></button>
                <div class="collapse navbar-collapse" id="navbarResponsive">
                    <ul class="navbar-nav ml-auto">
                        <li class="nav-item active"><a class="nav-link" href="#!">Home</a></li>
                        <li class="nav-item"><a class="nav-link" href="#!">About</a></li>
                        <li class="nav-item"><a class="nav-link" href="#!">Services</a></li>
                        <li class="nav-item"><a class="nav-link" href="#!">Contact</a></li>
                    </ul>
                </div>
            </div>
        </nav>
        <!-- Page Content-->
        <section>
            <div class="container px-4 px-lg-5">
                <div class="row gx-4 gx-lg-5">
                    <div class="col-lg-6">
                        <h1 class="mt-5">오늘의 메뉴</h1>
                        <p>OOO돈까스입니다.</p>
                    </div>
                </div>
            </div>
        </section>
    </body>
</html>

```

- server.xml 에서 설정한 path경로 + 이미지 파일이름 + 확장자를 경로에 적어줌.


[![image](https://user-images.githubusercontent.com/56810348/148181807-30638e96-bc0d-4873-9549-fb7de4cb6e7e.png)](https://user-images.githubusercontent.com/56810348/148181807-30638e96-bc0d-4873-9549-fb7de4cb6e7e.png)

- 이런 느낌으로 오늘의 식당과 메뉴를 보여줄려고 하는 생각임.

<h2>TODO</h2>
1. 회원가입 화면
2. 식당관리 화면
3. 메뉴 투표 화면
4. 오늘의 식당 길찾기 API 연동 후 소요시간과 위치 표출 화면
   
일단 이정도만 생각이 남. 추후 수정될 화면이나 기능이 있다면 계속 추가하며 글을 써보겠음.
