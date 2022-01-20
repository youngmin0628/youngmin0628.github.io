---
title:  "Spring Framework - 회사 점심 투표 웹사이트(4)"

categories:
  - Spring Framework

toc: true
toc_sticky: true
 
date: 2022-01-14
last_modified_at: 2022-01-19
---

<h1>회사 점심 투표 웹사이트(4)</h1>

기능을 조금 바꿨다.<br/>
메뉴조회와 추천을 함께하고 추천받은 식당리스트를 보여주는 화면으로 나누었고<br/>
가장 많은 추천을 받은 식당이  Main페이지로 보내진다.<br/>
마이페이지 화면은 삭제했다.(빨리 끝내고 다른거 하고싶어서..ㅎ)<br/>

[![image](https://user-images.githubusercontent.com/56810348/150081148-189282d4-17ec-4347-97c3-95d165830222.png)](https://user-images.githubusercontent.com/56810348/150081148-189282d4-17ec-4347-97c3-95d165830222.png)

- MenuList 화면은 이렇게 구성했다.


[![image](https://user-images.githubusercontent.com/56810348/150081199-aaee1c3d-766e-42b5-be4e-44efd7a1e4da.png)](https://user-images.githubusercontent.com/56810348/150081199-aaee1c3d-766e-42b5-be4e-44efd7a1e4da.png)

- KakaoAPI를 활용하여 지도검색을 하였음.


[![image](https://user-images.githubusercontent.com/56810348/150081247-d37c8fc9-79e2-4f56-9194-6e68721c4b9c.png)](https://user-images.githubusercontent.com/56810348/150081247-d37c8fc9-79e2-4f56-9194-6e68721c4b9c.png)


[![image](https://user-images.githubusercontent.com/56810348/150081392-0a151138-3ad6-4117-9f7e-f45670a3107f.png)](https://user-images.githubusercontent.com/56810348/150081392-0a151138-3ad6-4117-9f7e-f45670a3107f.png)

- 여기는 금일 추천된 메뉴리스트와 추천수가 보여진다.


```sql

CREATE TABLE `tb_menu_rec` (
  `rec_date` date NOT NULL COMMENT '추천날짜',
  `rec_user` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '추천자',
  `rec_id` varchar(100) DEFAULT NULL COMMENT '메뉴ID',
  PRIMARY KEY (`rec_date`,`rec_user`)
);

```

- 우선 추천받은 테이블을 생성해준다.

[![image](https://user-images.githubusercontent.com/56810348/150081859-6597f99f-660a-4dd0-af88-4a6443d93b46.png)](https://user-images.githubusercontent.com/56810348/150081859-6597f99f-660a-4dd0-af88-4a6443d93b46.png)

- 서버쪽 구성이다.(혹시나 까먹고 안올린게 있을까봐..)


[![image](https://user-images.githubusercontent.com/56810348/150081996-63cac411-d165-4d40-9210-5ed3e8d25a2c.png)](https://user-images.githubusercontent.com/56810348/150081996-63cac411-d165-4d40-9210-5ed3e8d25a2c.png)

- View단 구성이다.


일단 Main 패키지 부분은 이전 글과 동일하게 Controller 부분만 작성되있고 Service, DAO 부분은 아직도 만든게 없다... <br/>
(나중에 대쉬보드같은 통계를 나타내는 코드를 작성할수도 있으니 일단 남겨놓고 다음 Menu 패키지로 넘어가겟다.)<br/>
혹시 혼란스러워하는 사람이 있을수도 있으니 아무것도 없긴하지만 올려놓긴 하겠다.

**MainServiceImpl**

[![image](https://user-images.githubusercontent.com/56810348/150082714-0cc88491-124c-4ab6-bbb6-2b126bb5e93a.png)](https://user-images.githubusercontent.com/56810348/150082714-0cc88491-124c-4ab6-bbb6-2b126bb5e93a.png)

**MainService**
[![image](https://user-images.githubusercontent.com/56810348/150082924-faf573aa-58dc-43fb-884f-8052ee7328c2.png)](https://user-images.githubusercontent.com/56810348/150082924-faf573aa-58dc-43fb-884f-8052ee7328c2.png)

**MainDAO**
[![image](https://user-images.githubusercontent.com/56810348/150082986-c92d1ab5-5d34-4413-b8f6-eb3e77f78f2b.png)](https://user-images.githubusercontent.com/56810348/150082986-c92d1ab5-5d34-4413-b8f6-eb3e77f78f2b.png)

- 암것도없음..ㅎ

user패키지 부분도 그동안 수정한게 없었으므로 서버쪽 소스는 이전글을 참고하면된다.<br/>
그럼 이제 menu쪽 소스를 하나하나 올려보겠음.

**MenuController**
```java

package com.ym.menu.controller;

import java.util.Map;

import javax.inject.Inject;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import com.ym.menu.service.MenuService;

@Controller
public class MenuController {
	
	@Inject
	MenuService menuSerivce;
	
	@GetMapping(value="/menu/menuList")
	public String getMenuListPage() throws Exception {
		return "menu/menuList";
	}
	
	@SuppressWarnings("unchecked")
	@PostMapping(value="/menu/getMenuList")
	@ResponseBody
	public Map<String, Object> getMenuList(HttpServletRequest request, Model model) throws Exception {
		
		HttpSession session = request.getSession();
		Map<String, Object> userMap = (Map<String, Object>) session.getAttribute("userMap");
		
		model.addAttribute("userMap", userMap);
		
		return menuSerivce.getMenuList();
	}
	
	@PostMapping(value="/menu/menuInsertPost")
	@ResponseBody
	public Map<String, Object> menuInsertPost(@RequestParam Map<String, Object> requestMap) throws Exception {
		return menuSerivce.menuInsertPost(requestMap);
	}
	
	@PostMapping(value="/menu/menuRecPost")
	@ResponseBody
	public Map<String, Object> menuRecPost(@RequestParam Map<String, Object> requestMap) throws Exception {
		return menuSerivce.menuRecPost(requestMap);
	}
	
	@GetMapping(value="/menu/menuVote")
	public String getMenuVotePage() throws Exception {
		return "menu/menuVote";
	}
	
	@PostMapping(value="/menu/getVoteMenuList")
	@ResponseBody
	public Map<String, Object> getVoteMenuList() throws Exception {
		return menuSerivce.getVoteMenuList();
	}
}

```

**MenuService**

```java

package com.ym.menu.service;

import java.util.Map;

public interface MenuService {

	public Map<String, Object> getMenuList() throws Exception;
	
	public Map<String, Object> menuInsertPost(Map<String, Object> requestMap) throws Exception;
	
	public Map<String, Object> menuRecPost(Map<String, Object> requestMap) throws Exception;
	
	public Map<String, Object> getVoteMenuList() throws Exception;
	
}

```

**MenuServiceImpl**

```java

package com.ym.menu.service.impl;

import java.util.HashMap;
import java.util.List;
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
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		
		List<Map<String, Object>> resultMapList = menuDAO.getMenuList(); 
		
		resultMap.put("data", resultMapList);
		
		return resultMap;
	}
	
	@Override
	public Map<String, Object> menuInsertPost(Map<String, Object> requestMap) throws Exception {
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		
		int checkNo = menuDAO.menuDuplicationCheck(requestMap);
		
		if(checkNo != 0) {
			resultMap.put("rst_cd", "001");
			resultMap.put("rst_msg", "중복메뉴입니다.");
		} else {
			if(menuDAO.menuInsertPost(requestMap) <= 0) {
				resultMap.put("rst_cd", "001");
				resultMap.put("rst_msg", "등록에 실패하였습니다.");
			} else {
				resultMap.put("rst_cd", "000");
				resultMap.put("rst_msg", "등록이 완료되었습니다.");
			}
		}
		
		return resultMap;
	}
	
	@Override
	public Map<String, Object> menuRecPost(Map<String, Object> requestMap) throws Exception {
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		
		int checkTodayRec = menuDAO.menuTodayRecCheck(requestMap);
		
		if(checkTodayRec == 0) {
			if(menuDAO.menuRecPost(requestMap) <= 0) {
				resultMap.put("rst_cd", "001");
				resultMap.put("rst_msg", "시스템 오류로 추천에 실패하였습니다.");
			} else {
				resultMap.put("rst_cd", "000");
				resultMap.put("rst_msg", "메뉴추천이 되었습니다.");
			}
		} else {
			resultMap.put("rst_cd", "001");
			resultMap.put("rst_msg", "금일 메뉴 추천을 이미 하였습니다.");
		}
		
		return resultMap;
	}
	
	@Override
	public Map<String, Object> getVoteMenuList() throws Exception {
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		
		List<Map<String, Object>> resultMapList = menuDAO.getVoteMenuList(); 
		
		resultMap.put("resultList", resultMapList);
		
		return resultMap;
	}
}

```

**MenuDAO**

```java

package com.ym.menu.dao;

import java.util.List;
import java.util.Map;

import javax.inject.Inject;

import org.apache.ibatis.session.SqlSession;
import org.springframework.stereotype.Repository;

@Repository
public class MenuDAO {

	@Inject
	SqlSession sqlSession;
	
	public List<Map<String, Object>> getMenuList() throws Exception {
		
		return sqlSession.selectList("com.ym.menu.dao.MenuDAO.getMenuList");
	}
	
	public int menuInsertPost(Map<String, Object> requestMap) throws Exception {
		
		return sqlSession.insert("com.ym.menu.dao.MenuDAO.menuInsertPost", requestMap);
	}
	
	public int menuDuplicationCheck(Map<String, Object> requestMap) throws Exception {
		
		return sqlSession.selectOne("com.ym.menu.dao.MenuDAO.menuDuplicationCheck", requestMap);
	}
	
	public int menuRecPost(Map<String, Object> requestMap) throws Exception {
		return sqlSession.insert("com.ym.menu.dao.MenuDAO.menuRecPost", requestMap);
	}
	
	public int menuTodayRecCheck(Map<String, Object> requestMap) throws Exception {
		return sqlSession.selectOne("com.ym.menu.dao.MenuDAO.menuTodayRecCheck", requestMap);
	}
	
	public List<Map<String, Object>> getVoteMenuList() throws Exception {
		return sqlSession.selectList("com.ym.menu.dao.MenuDAO.getVoteMenuList");
	}
}

```

**Menu_SQL.xml**

```xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ym.menu.dao.MenuDAO">

	<!-- 식당 리스트 SELECT -->
	<select id="getMenuList" resultType="map">
		SELECT
			rstrnt_id,
			rstrnt_nm,
			rstrnt_cl,
			rstrnt_lc,
			kakao_map_id
		FROM tb_rstrnt
	</select>
	
	<insert id="menuInsertPost" parameterType="map">
		INSERT INTO 
			tb_rstrnt 
			(
				rstrnt_nm,
				rstrnt_cl,
				rstrnt_lc,
				kakao_map_id 
			)
		VALUES 
			(
				#{rstrnt_nm},
				#{rstrnt_cl},
				#{rstrnt_lc},
				#{kakao_map_id}
			)
	</insert>
	
	<select id="menuDuplicationCheck" parameterType="map" resultType="int">
		SELECT COUNT(rstrnt_id)
		FROM tb_rstrnt
		WHERE kakao_map_id = #{kakao_map_id}
	</select>
	
	<insert id="menuRecPost" parameterType="map">
		INSERT INTO 
			tb_menu_rec
			(
				rec_date,
				rec_user,
				rec_id
			)
		VALUES
			(
				now(),
				#{rec_user},
				#{rec_id}
			)
	</insert>
	
	<select id="menuTodayRecCheck" parameterType="map" resultType="int">
		SELECT COUNT(*)
		FROM tb_menu_rec
		WHERE 1=1
		AND   rec_user = #{rec_user}
		AND   rec_date = CURDATE()
	</select>
	
	<select id="getVoteMenuList" resultType="map">
		SELECT 
			tr.rstrnt_nm,
			tr.rstrnt_cl,
			tr.rstrnt_lc,
			COUNT(tr.rstrnt_id) as vote_cnt
		FROM tb_rstrnt tr 
		INNER JOIN tb_menu_rec tmr 
		ON tr.rstrnt_id = tmr.rec_id
		WHERE tmr.rec_date = CURDATE()
		GROUP BY tr.rstrnt_id
		ORDER BY vote_cnt DESC
	</select>
	
</mapper>

```

- 이렇게 모든 서버쪽소스는 끝이났다.

view쪽 작성 전 Kakao Developers에 가입을 해준다.

[카카오 개발자센터 링크](https://developers.kakao.com/)

[![image](https://user-images.githubusercontent.com/56810348/150084297-0cda442a-0a91-48dd-aad0-5eb1884f2462.png)](https://user-images.githubusercontent.com/56810348/150084297-0cda442a-0a91-48dd-aad0-5eb1884f2462.png)

- 로그인을 하고 내 어플리케이션 클릭.


[![image](https://user-images.githubusercontent.com/56810348/150084421-56d724f8-d6a2-4246-a7c3-19f767430a22.png)](https://user-images.githubusercontent.com/56810348/150084421-56d724f8-d6a2-4246-a7c3-19f767430a22.png)

- 내 어플리케이션 추가하기 클릭


[![image](https://user-images.githubusercontent.com/56810348/150084611-6e7efe54-cec0-4c1c-b4d9-1773fc7c88ff.png)](https://user-images.githubusercontent.com/56810348/150084611-6e7efe54-cec0-4c1c-b4d9-1773fc7c88ff.png)

- 이렇게 대충 만들어준 뒤에 저장하기 클릭


[![image](https://user-images.githubusercontent.com/56810348/150084725-be9a6d8f-4480-4a38-8698-77472d18dce2.png)](https://user-images.githubusercontent.com/56810348/150084725-be9a6d8f-4480-4a38-8698-77472d18dce2.png)

- 짜잔~ 클릭!

[![image](https://user-images.githubusercontent.com/56810348/150084867-57d38791-904d-4096-a80c-c22aa83b80c1.png)](https://user-images.githubusercontent.com/56810348/150084867-57d38791-904d-4096-a80c-c22aa83b80c1.png)

- 이 key가 우리가 카카오맵을 쓸때 사용할 key다.

다시 이클립스로 돌아와서

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
    
    <body style="overflow-x: hidden;">

```

**navbar.jsp**

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
	<nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-bottom" id="navbar_ui">
	    <div class="container px-4 px-lg-5">
	    	<a class="navbar-brand" href="/">Home</a>
	        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarResponsive" aria-controls="navbarResponsive" aria-expanded="false" aria-label="Toggle navigation"><span class="navbar-toggler-icon"></span></button>
	        <div class="collapse navbar-collapse" id="navbarResponsive">
	        	<ul class="navbar-nav ml-auto">
	        	<c:choose>
	        		<c:when test = "${userMap != null}">
	            		<!-- <li class="nav-item"><a class="nav-link" href="/user/myInfoPage">마이페이지</a></li> -->
	            		<li class="nav-item"><a class="nav-link" href="javascript:signOutPost();">로그아웃</a></li>
	            		<li class="nav-item"><a class="nav-link" href="/menu/menuList">식당조회</a></li>
	            		<li class="nav-item"><a class="nav-link" href="/menu/menuVote">추천결과</a></li>
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

**menuList.jsp**

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
	<%@ include file="/WEB-INF/include/header.jsp" %>

	<%@ include file="/WEB-INF/include/navbar.jsp" %>

	<style>
		html,
		body,
		.intro {
			height: 100%;
		}
		
		table td,
		table th {
			text-overflow: ellipsis;
			white-space: nowrap;
			overflow: hidden;
		}
		
		thead th,
		tbody th {
			color: #fff;
		}
		
		tbody td {
			font-weight: 500;
			color: rgba(255,255,255,.65);
		}
		
		.card {
			border-radius: .5rem;
		}
		
		.mask-custom {
			background: rgba(24, 24, 16, .2);
			border-radius: 2em;
			backdrop-filter: blur(25px);
			border: 2px solid rgba(255, 255, 255, 0.05);
			background-clip: padding-box;
			box-shadow: 10px 10px 10px rgba(46, 54, 68, 0.03);
		}
	</style>
	
	<script type="text/javascript" src="//dapi.kakao.com/v2/maps/sdk.js?appkey=JavaScript 키&libraries=services"></script>
	<script type="text/javascript" src="https://cdn.datatables.net/1.11.3/js/jquery.dataTables.min.js"></script>
	<link href="https://cdn.datatables.net/1.11.3/css/jquery.dataTables.min.css" rel="stylesheet"/>
	<script>
	
		var login_id = '${userMap.user_id}';	
	
		var map, 
			infowindow,
			mapContainer,
			ps;	
		
		var category_group_name;
		
		$( document ).ready(function() {
		    
			//모달 팝업이 완전히 열린 뒤 실행
			$('#insertMenuModal').on('shown.bs.modal', function (e) {
				openKakaoMap();
			});
			
			//모달 팝업이 닫힐떄 실행
			$('#insertMenuModal').on('hidden.bs.modal', function (e) {
				$("#searchRstrnt").val("");
				map.setCenter(new kakao.maps.LatLng(37.566826, 126.9786567));
				map.setLevel(3);
			});
			
			menuList();
			
		});
		
		//Kakao 개발소스 긁어옴
		function openKakaoMap(searchKey) {
			
			// 마커를 클릭하면 장소명을 표출할 인포윈도우 입니다
			infowindow = new kakao.maps.InfoWindow({zIndex:1});
		
			mapContainer = document.getElementById('map'), // 지도를 표시할 div 
			    mapOption = {
			        center: new kakao.maps.LatLng(37.566826, 126.9786567), // 지도의 중심좌표
			        level: 3 // 지도의 확대 레벨
			    };  
		
			// 지도를 생성합니다    
			map = new kakao.maps.Map(mapContainer, mapOption); 
		
			// 장소 검색 객체를 생성합니다
			ps = new kakao.maps.services.Places(); 
			
			if(searchKey != null && searchKey != "") {
				// 키워드로 장소를 검색합니다
				ps.keywordSearch(searchKey, placesSearchCB); 
			}
			
			map.relayout();
			
			// 키워드 검색 완료 시 호출되는 콜백함수 입니다
			function placesSearchCB (data, status, pagination) {
				
			    if (status === kakao.maps.services.Status.OK) {
			        // 검색된 장소 위치를 기준으로 지도 범위를 재설정하기위해
			        // LatLngBounds 객체에 좌표를 추가합니다
			        var bounds = new kakao.maps.LatLngBounds();
		
			        for (var i=0; i<data.length; i++) {
			            displayMarker(data[i]);    
			            bounds.extend(new kakao.maps.LatLng(data[i].y, data[i].x));
			        }       
		
			        // 검색된 장소 위치를 기준으로 지도 범위를 재설정합니다
			        map.setBounds(bounds);
			    }
			    
			}
		
			// 지도에 마커를 표시하는 함수입니다
			function displayMarker(place) {
				
			    // 마커를 생성하고 지도에 표시합니다
			    var marker = new kakao.maps.Marker({
			        map: map,
			        position: new kakao.maps.LatLng(place.y, place.x) 
			    });
		
			    // 마커에 클릭이벤트를 등록합니다
			    kakao.maps.event.addListener(marker, 'click', function() {
			        // 마커를 클릭하면 장소명이 인포윈도우에 표출됩니다
			        infowindow.setContent('<div style="padding:5px;font-size:12px;">' + place.place_name + '</div>');
			        infowindow.open(map, marker);
			        
			        //여기 값 넣어주는 곳만 손봄
			        $("#category_group_name").val(place.category_group_name);
			        $("#rstrnt_nm").val(place.place_name);
			        $("#rstrnt_cl").val(place.category_name);
			        $("#rstrnt_lc").val(place.address_name);
			        $("#kakao_map_id").val(place.id);
				});
			}
			
		}
		
		function searchRstrnt(e) {
			
			openKakaoMap($(e).val());
			
		}
		
		function insertMenu() {
			
			if($("#category_group_name").val() != "음식점") {
				alert("음식점이 아닙니다.");
				return false;
			}
			
			var param = {
				rstrnt_nm : $("#rstrnt_nm").val(),
				rstrnt_cl : $("#rstrnt_cl").val(),
				rstrnt_lc : $("#rstrnt_lc").val(),
				kakao_map_id : $("#kakao_map_id").val()
			};
			
			$.ajax({
				type:"POST",
				url:"/menu/menuInsertPost",
				data:param,
				success:function(data) {
					
					alert(data.rst_msg);
					
					if(data.rst_cd == "001")
					{
						return false;
					} else {
						$('#insertMenuModal').modal("hide");
						$('#menuListTable').DataTable().ajax.reload();	
					}
				},
				error:function() {
					alert("통신오류");
				}
			});
			
		}
		
		function menuList() {
			
			$('#menuListTable').DataTable({
		        "paging" : true,
		        "searching" : false,
		        "info" : false,
		        "ordering" : false,
		        "lengthChange" : false,
		        "pageLength" : 10,
		        "columns" : 
					[ 
						{data : "rstrnt_id"},
						{data : "rstrnt_nm"},
						{data : "rstrnt_cl"},
						{data : "rstrnt_lc"},
						{data : "kakao_map_id"}
					],
				"columnDefs" : 
					[	
						{
							targets: [ 0 ],
							visible: false
						},
						{
					      	"targets": 4,
					      	"data": null,
							"render": function(data, type, row)
								{
									return '<button type="button" class="btn btn-success btn-sm px-3" onclick="rstrntRec(' + row.rstrnt_id + ');"><i class="fas fa-check"></i></button>';
								}
						}
					],
		        ajax: {
		    		url : "/menu/getMenuList",
		            method: "POST",
		     		cache : false,
		     		dataType	: "json",
		     		error	: function(request, status, error){
		     	        alert("code:"+request.status+"\n"+"message:"+request.responseText+"\n"+"error:"+error);
		     		}
		        }
		    });
			
		}
		
		function rstrntRec(rstrnt_id){
			
			var param = {
				rec_id : rstrnt_id,
				rec_user : login_id
			};
			
			if(confirm("추천하시겠습니까?")) {
				$.ajax({
					type:"POST",
					url:"/menu/menuRecPost",
					data:param,
					success:function(data) {
						
						alert(data.rst_msg);
						
						if(data.rst_cd == "001")
						{
							return false;
						} 
					},
					error:function() {
						alert("통신오류");
					}
				});
			}
			
		}
		
	</script>
	
	<section class="intro">
		<div class="bg-image h-100" style="background-image: url('https://mdbootstrap.com/img/Photos/new-templates/tables/img3.jpg');">
			<div class="mask d-flex align-items-center h-100" style="background-color: rgba(0,0,0,.25);">
				<div class="container">
					<div class="row justify-content-center">
						<div class="col-12">
							<div class="card bg-dark shadow-2-strong">
								<div class="card-body">
									<div class="table-responsive" style="text-align: center; overflow: hidden">
										<button type="button" class="btn btn-outline-primary btn-rounded float-end" data-mdb-ripple-color="dark" data-mdb-toggle="modal" data-mdb-target="#insertMenuModal">식당 등록</button>
										<table class="table table-dark table-borderless mb-0" id="menuListTable">
											<colgroup>
												<col style="width: 20%;">
												<col style="width: 35%;">
												<col style="width: 35%;">
												<col style="width: 10%;">
											</colgroup>
											<thead>
												<tr>
													<th scope="col">식당 고유ID</th>
													<th scope="col">식당 이름</th>
													<th scope="col">식당 분류</th>
													<th scope="col">식당 위치</th>
													<th scope="col">추천</th>
												</tr>
											</thead>
											<tbody>
											</tbody>
										</table>
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
				
				<!-- Modal -->
				<div class="modal fade" id="insertMenuModal" tabindex="-1" aria-labelledby="inserMenuModalLabel" aria-hidden="true">
					<div class="modal-dialog">
						<div class="modal-content">
							<div class="modal-header">
								<h5 class="modal-title" id="inserMenuModalLabel">식당 등록하기</h5>
								<button type="button" class="btn-close" data-mdb-dismiss="modal" aria-label="Close"></button>
							</div>
							<div class="modal-body">
								<div class="input-group mb-3">
									<input type="text" class="form-control" placeholder="식당 검색..." aria-label="search..." id="searchRstrnt" oninput="searchRstrnt(this);" autocomplete="off"/>
								</div>
								<div id="map" style="width: 450px; height: 500px;"></div>
								<input type="hidden" id="rstrnt_nm" name="rstrnt_nm" />
								<input type="hidden" id="rstrnt_cl" name="rstrnt_cl" />
								<input type="hidden" id="rstrnt_lc" name="rstrnt_lc" />
								<input type="hidden" id="category_group_name" name="category_group_name" />
								<input type="hidden" id="kakao_map_id" name="kakao_map_id" />
							</div>
							<div class="modal-footer">
								<button type="button" class="btn btn-secondary" data-mdb-dismiss="modal">닫기</button>
								<button type="button" class="btn btn-primary" onclick="insertMenu();">등록완료</button>
							</div>
						</div>
					</div>
				</div>
				
			</div>
		</div>
	</section>

	<%@ include file="/WEB-INF/include/footer.jsp" %>

```

[![image](https://user-images.githubusercontent.com/56810348/150086676-e9acb03f-751b-4f50-a9d4-2a2b20d65c47.png)](https://user-images.githubusercontent.com/56810348/150086676-e9acb03f-751b-4f50-a9d4-2a2b20d65c47.png)

- 여기서 저 부분에 아까 받은 key값을 복사해서 넣어준다.

**menuVote.jsp**

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
	<%@ include file="/WEB-INF/include/header.jsp" %>

	<%@ include file="/WEB-INF/include/navbar.jsp" %>
	
	<script>
		
		$(document).ready(function(){
			menuVoteList();
		});
		
		//스크롤이 다 내려갔을 때 실행
		$(window).scroll(function(){
			if($(document).height() > $(window).height()) {
				if($(window).scrollTop() == $(document).height() - $(window).height())
			    { 
			    	$("#navbar_ui").css('display', "none");
				} 
				else 
				{ 
					$("#navbar_ui").css('display', "block");
				}
			}
		}); 
		
		function menuVoteList(){
			
			$.ajax({
				type:"POST",
				url:"/menu/getVoteMenuList",
				success:function(data) {
					
					var dataList = data.resultList;
					var html = "";
					
					if(dataList != '' && dataList != null) {
						for(var i = 0; i < dataList.length; i++) {
							html =  '<div class="row">';
							html += 	'<div class="container py-5 h-100">';
							html += 		'<div class="row d-flex justify-content-center align-items-center h-100">';
							html += 			'<div class="col-md-6 col-xl-5">';
							html += 				'<div class="card text-white" style="background-color: #1f959b; border-radius: 15px;">';
							html += 					'<div class="card-body p-5">';
							html += 						'<i class="fas fa-utensils fa-2x mb-4"></i>';
							html += 						'<p class="lead">' + dataList[i].rstrnt_nm + '</p><hr>';
							html += 						'<div class="d-flex justify-content-between">';
							html += 							'<p class="mb-0">' + dataList[i].rstrnt_cl + '</p>';
							html += 							'<h6 class="mb-0">';
							html += 								'<span class="badge rounded-pill" style="background-color: rgba(0,0,0, 0.2);">' + dataList[i].vote_cnt + '</span>';
							html += 								'<i class="fas fa-heart ms-2"></i>';
							html += 							'</h6>';
							html += 						'</div>';
							html += 						'<div class="d-flex justify-content-between">';
							html += 							'<p class="mb-0">' + dataList[i].rstrnt_lc + '</p>';
							html += 						'</div>';
							html += 					'</div>';
							html += 				'</div>';
							html += 			'</div>';
							html += 		'</div>';
							html += 	'</div>';
							html += '</div>';
							
							$("#menuVoteList").append(html);
						}
					} else {
						html =  '<div class="row">';
						html += 	'<div class="container py-5 h-100">';
						html += 		'<div class="row d-flex justify-content-center align-items-center h-100">';
						html += 			'<div class="col-md-6 col-xl-5">';
						html += 				'<div class="card text-white" style="background-color: #1f959b; border-radius: 15px;">';
						html += 					'<div class="card-body p-5">';
						html += 						'<i class="fas fa-utensils fa-2x mb-4"></i>';
						html += 						'<p class="lead">금일 결과가 없습니다.</p><hr>';
						html += 						'<div class="d-flex justify-content-between">';
						html += 							'<p class="mb-0"></p>';
						html += 							'<h6 class="mb-0">';
						html += 								'<span class="badge rounded-pill" style="background-color: rgba(0,0,0, 0.2);"></span>';
						html += 								'<i class="fas fa-heart ms-2"></i>';
						html += 							'</h6>';
						html += 						'</div>';
						html += 						'<div class="d-flex justify-content-between">';
						html += 							'<p class="mb-0"></p>';
						html += 						'</div>';
						html += 					'</div>';
						html += 				'</div>';
						html += 			'</div>';
						html += 		'</div>';
						html += 	'</div>';
						html += '</div>';
						
						$("#menuVoteList").append(html);
					}
				},
				error:function() {
					alert("통신오류");
				}
			});
			
		}
		
	</script>
	
	<section class="vh-100 bg-light" id="menuVoteList"></section>
	
	<%@ include file="/WEB-INF/include/footer.jsp" %>

```

- 이렇게 해주면 적용완료!

**userSignIn.jsp**

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
	<%@ include file="/WEB-INF/include/header.jsp" %>

	<%@ include file="/WEB-INF/include/navbar.jsp" %>
	
	<script>
	
		$( document ).ready(function() {
		    $("#user_pw").keypress(function (key) {
		    	if(key.keyCode == 13) {
		    		signInPost();
		    	}
		    });
		});	
	
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

- enterKey 이벤트도 추가해줬으므로 이것도 복붙!

<h2>TODO</h2>
1. 로그인 체크 후 페이지이동
2. 오전 11시 Schedule을 돌려서 가장 많이 받은 식당 Main.jsp에 표출(11시 이후엔 추천못하게 적용)
3. 로그데이터 저장기능
4. 추천수가 동일할 시 랜덤으로 표출
5. 회원가입시 비밀번호 암호화기능

차근차근 또 시간나면 작성해보도록 하겠다.