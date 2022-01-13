---
title:  "Spring Framework - 회사 점심 투표 웹사이트(3)"

categories:
  - Spring Framework

toc: true
toc_sticky: true
 
date: 2022-01-04
last_modified_at: 2022-01-12
---

<h1>회사 점심 투표 웹사이트(3)</h1>


설명은 이제 줄이고 코드위주로 올려볼려고한다.(남는 시간에 작성하는거라 너무 자세하게 적어서 힘들었음.)


[![image](https://user-images.githubusercontent.com/56810348/148910087-2b9eb368-ef67-400d-98b5-69a5a77a6c15.png)](https://user-images.githubusercontent.com/56810348/148910087-2b9eb368-ef67-400d-98b5-69a5a77a6c15.png)

- 많은 소스들이 추가되었다. 똑같이 만들어준다.


[![image](https://user-images.githubusercontent.com/56810348/148910325-530030aa-8c22-41b7-9d2d-a5ed95a9ff92.png)](https://user-images.githubusercontent.com/56810348/148910325-530030aa-8c22-41b7-9d2d-a5ed95a9ff92.png)

- view 쪽도 header, nav, footer 추가하고 여러가지 추가되었다.


```xml

    <!-- jackson --> 
		<dependency> 
			<groupId>com.fasterxml.jackson.core</groupId> 
			<artifactId>jackson-core</artifactId> 
			<version>2.9.2</version> 
		</dependency>
		
		<!-- jackson-core --> 
		<dependency> 
			<groupId>com.fasterxml.jackson.core</groupId> 
			<artifactId>jackson-databind</artifactId> 
			<version>2.9.2</version> 
		</dependency>

```

- pom.xml에도 json데이터를 위해 pom에 추가해준다.

**MainController**

```java

package com.ym.main.controller;

import java.util.Map;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class MainController {

	@SuppressWarnings("unchecked")
	@GetMapping(value="/")
	public String MainPage(HttpServletRequest request, Model model) throws Exception {
		
		HttpSession session = request.getSession();
		Map<String, Object> userMap = (Map<String, Object>) session.getAttribute("userMap");
		
		model.addAttribute("userMap", userMap);
		
		return "main";
	}
	
}

```

- MainService, MainServiceImpl, MainDAO는 아직 추가한 소스가 없으므로 PASS


**MenuController**

```java

package com.ym.menu.controller;

import javax.inject.Inject;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import com.ym.menu.service.MenuService;

@Controller
public class MenuController {
	
	@Inject
	MenuService menuSerivce;
	
	@GetMapping(value="/menu/menuList")
	public String getMenuListPage() throws Exception {
		return "menu/menuList";
	}
	
}

```

**MenuService**

```java

package com.ym.menu.service;

import java.util.Map;

public interface MenuService {

	public Map<String, Object> getMenuList() throws Exception;
}

```

**MenuServiceImpl**

```java
package com.ym.menu.service.impl;

import java.util.Map;

import javax.inject.Inject;

import org.springframework.stereotype.Service;

import com.ym.menu.dao.MenuDAO;
import com.ym.menu.service.MenuService;

@Service
public class MenuServiceImpl implements MenuService{
	
	@Inject
	MenuDAO menuDAO;
	
	@Override
	public Map<String, Object> getMenuList() throws Exception {
		
		return menuDAO.getMenuList();
	}
}

```

**MenuDAO**

```java

package com.ym.menu.dao;

import java.util.Map;

import javax.inject.Inject;

import org.apache.ibatis.session.SqlSession;
import org.springframework.stereotype.Repository;

@Repository
public class MenuDAO {

	@Inject
	SqlSession sqlsession;
	
	public Map<String, Object> getMenuList() throws Exception {
		
		return sqlsession.selectOne("com.ym.menu.dao.MenuDAO.getMenuList");
	}
}

```

**Menu_SQL.xml**

```xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ym.menu.dao.MenuDAO">

	<!-- 메뉴 리스트 SELECT -->
	<select id="getMenuList" resultType="map">
		SELECT
			rstrnt_id,
			rstrnt_nm,
			rstrnt_cl,
			rstrnt_lc
		FROM tb_rstrnt
	</select>

</mapper>

```

- menuList 기능도 아직 개발중이라 저대로만 하고 일단 냅둠.

```sql

CREATE TABLE `tb_user` (
  `seq` int NOT NULL AUTO_INCREMENT COMMENT '일련번호',
  `user_id` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '사용자 ID',
  `user_pw` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '사용자 패스워드',
  `reg_date` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '가입일자',
  `delete_at` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT 'N' COMMENT '삭제여부',
  `del_date` timestamp NULL DEFAULT NULL COMMENT '삭제일자',
  PRIMARY KEY (`seq`)
);

```

- 테이블을 먼저 생성해줌.

**UserController**

```java

package com.ym.user.controller;

import java.util.HashMap;
import java.util.Map;

import javax.inject.Inject;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import com.ym.user.service.UserService;

@Controller
public class UserController {
	
	@Inject
	UserService userService;
	
	@GetMapping("/user/signUpPage")
	public String signUpPage() throws Exception {
		return "user/userSignUp";
	}
	
	@PostMapping("/user/signUpPost")
	@ResponseBody
	public Map<String, Object> signUpPost(@RequestParam Map<String, Object> requestMap) throws Exception {
		return userService.signUpPost(requestMap);
	}
	
	@GetMapping("/user/signInPage")
	public String signInPage() throws Exception {
		return "user/userSignIn";
	}
	
	@PostMapping("/user/signInPost")
	@ResponseBody
	public Map<String, Object> signInPost(@RequestParam Map<String, Object> requestMap, HttpServletRequest request) throws Exception {		
		return userService.signInPost(requestMap, request);
	}
	
	@PostMapping("/user/signOutPost")
	@ResponseBody
	public Map<String, Object> signOutPost(HttpServletRequest request) {
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		HttpSession session = request.getSession();
		try 
		{
			session.invalidate();
			resultMap.put("rst_cd","000");
			resultMap.put("rst_msg","로그아웃되었습니다.");
		}
		catch (Exception e) 
		{
			resultMap.put("rst_cd","001");
			resultMap.put("rst_msg","로그아웃 실패되었습니다.");
		}
		return resultMap;
	}
} 

```

**UserService**

```java

package com.ym.user.service;

import java.util.Map;

import javax.servlet.http.HttpServletRequest;

public interface UserService {
	
	//회원가입
	public Map<String, Object> signUpPost(Map<String, Object> requestMap) throws Exception;
	//로그인
	public Map<String, Object> signInPost(Map<String, Object> requestMap, HttpServletRequest request) throws Exception;
}

```

**UserServiceImpl**

```java

package com.ym.user.service.impl;

import java.util.HashMap;
import java.util.Map;

import javax.inject.Inject;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Service;

import com.ym.user.dao.UserDAO;
import com.ym.user.service.UserService;

@Service
public class UserServiceImpl implements UserService{
	
	@Inject
	UserDAO userDAO;
	
	//회원가입
	@Override
	public Map<String, Object>signUpPost(Map<String, Object> requestMap) throws Exception {
		
		Map<String ,Object> resultMap = new HashMap<String, Object>();
		
		//중복체크
		if(userDAO.duplication_check(requestMap) == 0) 
		{
			if(userDAO.signUpPost(requestMap) <= 0) 
			{
				resultMap.put("rst_cd","001");
				resultMap.put("rst_msg","내부 시스템 오류입니다.");
			}
			else 
			{
				resultMap.put("rst_cd","000");
				resultMap.put("rst_msg", "회원가입되었습니다.");
			}
		}
		else
		{
			resultMap.put("rst_cd","002");
			resultMap.put("rst_msg", "중복된 아이디입니다.");
		}
		
		return resultMap;
	}
	
	//로그인
	@Override
	public Map<String, Object>signInPost(Map<String, Object> requestMap, HttpServletRequest request) throws Exception {
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		
		if(userDAO.signInPost(requestMap) == 0) 
		{
			resultMap.put("rst_cd","001");
			resultMap.put("rst_msg","잘못된 정보입니다.");
		}
		else
		{
			resultMap.put("rst_cd","000");
			resultMap.put("rst_msg","로그인되었습니다.");
			
			HttpSession session = request.getSession();
			session.setAttribute("userMap", requestMap);
		}
		
		return resultMap;
	}
}

```

**UserDAO**

```java

package com.ym.user.dao;

import java.util.Map;

import javax.inject.Inject;

import org.apache.ibatis.session.SqlSession;
import org.springframework.stereotype.Repository;

@Repository
public class UserDAO {

	@Inject
	SqlSession sqlSession;
	
	public int signUpPost(Map<String ,Object> requestMap) throws Exception {
		return sqlSession.insert("com.ym.user.dao.UserDAO.signUpPost", requestMap);
	}
	
	public int duplication_check(Map<String, Object> requestMap) throws Exception {
		return sqlSession.selectOne("com.ym.user.dao.UserDAO.duplication_check", requestMap);
	}
	
	public int signInPost(Map<String, Object> requestMap) throws Exception {
		return sqlSession.selectOne("com.ym.user.dao.UserDAO.signInPost", requestMap);
	}
}

```

**User_SQL.xml**

```xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ym.user.dao.UserDAO">

	<insert id="signUpPost" parameterType="map">
		INSERT INTO tb_user 
			(
				user_id,
				user_pw
			)
		VALUES 
			(
				#{user_id},
				#{user_pw}
			)
	</insert>
	
	<select id="duplication_check" parameterType="map" resultType="int">
		SELECT COUNT(*)
		FROM tb_user
		WHERE user_id = #{user_id}
	</select>
	
	<select id="signInPost" parameterType="map" resultType="int">
		SELECT COUNT(*)
		FROM tb_user
		WHERE 1=1
		AND   user_id = #{user_id}
		AND   user_pw = #{user_pw}
	</select>
	
</mapper>

```

**header.jsp**

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
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
    
    <body>

```

**navbar.jsp**

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
	<nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-bottom">
	    <div class="container px-4 px-lg-5">
	    	<a class="navbar-brand" href="/">Home</a>
	        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarResponsive" aria-controls="navbarResponsive" aria-expanded="false" aria-label="Toggle navigation"><span class="navbar-toggler-icon"></span></button>
	        <div class="collapse navbar-collapse" id="navbarResponsive">
	        	<ul class="navbar-nav ml-auto">
	        	<c:choose>
	        		<c:when test = "${userMap != null}">
	            		<li class="nav-item"><a class="nav-link" href="/user/myInfoPage">마이페이지</a></li>
	            		<li class="nav-item"><a class="nav-link" href="javascript:signOutPost();">로그아웃</a></li>
	            		<li class="nav-item"><a class="nav-link" href="#!">메뉴투표</a></li>
	                	<li class="nav-item"><a class="nav-link" href="#!">메뉴조회</a></li>
	            	</c:when>
	            	<c:when test = "${userMap == null}">
	            		<li class="nav-item"><a class="nav-link" href="/user/signUpPage">회원가입</a></li>
	            		<li class="nav-item"><a class="nav-link" href="/user/signInPage">로그인</a></li>
	            	</c:when>	
	        	</c:choose>
				</ul>
			</div>
		</div>
	</nav>
	
	<script>
    	function signOutPost() {
    		
    		$.ajax({
				type:"POST",
				url:"/user/signOutPost",
				success:function(data) {
					
					alert(data.rst_msg);
					
					if(data.rst_cd == "001") 
					{
						return false;
					}
					
					location.href="/";
				},
				error:function() {
					alert("통신오류");
				}
			});
		}
	</script>

```

**footer.jsp**

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
	</body>
	<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mdb-ui-kit/3.10.1/mdb.min.js"></script>
	<!-- Font Awesome -->
	<link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.1/css/all.min.css" rel="stylesheet"/>
	<!-- Google Fonts -->
	<link href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700&display=swap" rel="stylesheet"/>
	<!-- MDB -->
	<link href="https://cdnjs.cloudflare.com/ajax/libs/mdb-ui-kit/3.10.1/mdb.min.css" rel="stylesheet"/>
</html>

```

[![image](https://user-images.githubusercontent.com/56810348/149426312-5b4acebf-aa81-49b2-be5f-76959402a928.png)](https://user-images.githubusercontent.com/56810348/149426312-5b4acebf-aa81-49b2-be5f-76959402a928.png)


**userSignUp.jsp**

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
	<%@ include file="/WEB-INF/include/header.jsp" %>

	<%@ include file="/WEB-INF/include/navbar.jsp" %>
	
	<script>
		function signUpPost() {
			
			var user_id= $("#user_id").val();
			var user_pw = $("#user_pw").val();
			var user_pw_confirm = $("#user_pw_confirm").val();
			
			//validation 체크
			if(user_id == '' || user_id == null) {
				$("#user_id").focus();
				alert("아이디를 입력해주세요.");
				return false;
			}
			
			if(user_pw == '' || user_pw == null) {
				$("#user_pw").focus();
				alert("비밀번호를 입력해주세요.");
				return false;
			}
			
			if(user_pw != user_pw_confirm) {
				$("#user_pw").focus();
				alert("비밀번호가 다릅니다.");
				return false;
			}
			
			var param = {
				user_id : $("#user_id").val(),
				user_pw : $("#user_pw").val(),
				user_pw_confirm : $("#user_pw_confirm").val()
			};
			
			$.ajax({
				type:"POST",
				url:"/user/signUpPost",
				data:param,
				success:function(data) {
					
					alert(data.rst_msg);
					
					if(data.rst_cd == "002") 
					{
						return false;
					}
					
					location.href="/";
				},
				error:function() {
					alert("통신오류");
				}
			});
		};
	</script>
	
	<section class="vh-100 gradient-custom">
		<div class="container py-5 h-100">
			<div class="row d-flex justify-content-center align-items-center h-100">
				<div class="col-12 col-md-8 col-lg-6 col-xl-5">
					<div class="card bg-dark text-white" style="border-radius: 1rem;">
						<div class="card-body p-5 text-center">
							<div class="mb-md-5 mt-md-4 pb-5">
								<h2 class="fw-bold mb-2 text-uppercase">회원가입</h2>
								<p class="text-white-50 mb-5">정보를 입력해주세요.</p>
								<div class="form-outline form-white mb-4">
									<input type="text" id="user_id" class="form-control form-control-lg" autocomplete="off"/>
									<label class="form-label" for="user_id">ID</label>
								</div>
								<div class="form-outline form-white mb-4">
									<input type="password" id="user_pw" class="form-control form-control-lg" />
									<label class="form-label" for="user_pw">Password</label>
								</div>
								<div class="form-outline form-white mb-4">
									<input type="password" id="user_pw_confirm" class="form-control form-control-lg" />
									<label class="form-label" for="user_pw_confirm">Password Confirm</label>
								</div>
								<button class="btn btn-outline-light btn-lg px-5" type="button" onclick="signUpPost();">회원가입</button>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</section>

	<%@ include file="/WEB-INF/include/footer.jsp" %>

```

[![image](https://user-images.githubusercontent.com/56810348/149426371-a784d5df-bc6d-4e81-af33-61b9a7f4e6b4.png)](https://user-images.githubusercontent.com/56810348/149426371-a784d5df-bc6d-4e81-af33-61b9a7f4e6b4.png)


**userSignIn.jsp**

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
	<%@ include file="/WEB-INF/include/header.jsp" %>

	<%@ include file="/WEB-INF/include/navbar.jsp" %>
	
	<script>
		function signInPost() {
			
			var user_id= $("#user_id").val();
			var user_pw = $("#user_pw").val();
			
			//validation 체크
			if(user_id == '' || user_id == null) {
				$("#user_id").focus();
				alert("아이디를 입력해주세요.");
				return false;
			}
			
			if(user_pw == '' || user_pw == null) {
				$("#user_pw").focus();
				alert("비밀번호를 입력해주세요.");
				return false;
			}
			
			var param = {
					user_id : $("#user_id").val(),
					user_pw : $("#user_pw").val()
				};
			
			$.ajax({
				type:"POST",
				url:"/user/signInPost",
				data:param,
				success:function(data) {
					
					alert(data.rst_msg);
					
					if(data.rst_cd == "001") 
					{
						return false;
					}
					
					location.href = "/";
				},
				error:function() {
					alert("통신오류");
				}
			});
		}
	</script>
	
	<section class="vh-100 gradient-custom">
		<div class="container py-5 h-100">
			<div class="row d-flex justify-content-center align-items-center h-100">
				<div class="col-12 col-md-8 col-lg-6 col-xl-5">
					<div class="card bg-dark text-white" style="border-radius: 1rem;">
						<div class="card-body p-5 text-center">
							<div class="mb-md-5 mt-md-4 pb-5">
								<h2 class="fw-bold mb-2 text-uppercase">Login</h2>
								<p class="text-white-50 mb-5">ID와 PASSWORD를 입력하세요.</p>
								<div class="form-outline form-white mb-4">
									<input type="text" id="user_id" class="form-control form-control-lg" autocomplete="off"/>
									<label class="form-label" for="user_id">ID</label>
								</div>
								<div class="form-outline form-white mb-4">
									<input type="password" id="user_pw" class="form-control form-control-lg" />
									<label class="form-label" for="user_pw">Password</label>
								</div>
								<!-- <p class="small mb-5 pb-lg-2"><a class="text-white-50" href="#!">Forgot password?</a></p> -->
								<button class="btn btn-outline-light btn-lg px-5" type="button" onclick="signInPost();">Login</button>
							</div>
							<div>
								<p class="mb-0">계정이 없으신가요? <a href="/user/signUpPage" class="text-white-50 fw-bold">회원가입</a></p>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</section>
	
	<%@ include file="/WEB-INF/include/footer.jsp" %>

```

**style.css**

```css

.navbar-dark .navbar-toggler-icon {
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 30 30'%3e%3cpath stroke='rgba%28255, 255, 255, 0.55%29' stroke-linecap='round' stroke-miterlimit='10' stroke-width='2' d='M4 7h22M4 15h22M4 23h22'/%3e%3c/svg%3e") !important;
}

```

- style.css 파일에서 해당 클래스의 !important를 적어준다. 모바일화면을 위해서!

- **menuList.jsp 는 아직 작업중이라 올리지않음.**

<h2>추후에 spring-security를 적용할 예정</h2>
