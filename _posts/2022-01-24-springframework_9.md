---
title:  "Spring Framework - 회사 점심 투표 웹사이트(5)"

categories:
  - Spring Framework

toc: true
toc_sticky: true
 
date: 2022-01-24
last_modified_at: 2022-01-25
---

<h1>회사 점심 투표 웹사이트(5)</h1>

아마 이번 포스트가 마지막이 될 것같다. 필요한 기능들은 동작되게만들었으니 나머지는 시간이 또 된다면 덧붙여보거나 다른 환경을 쓴 프로젝트를 만들것같다.
<br/>
그럼 시작하겠다.

[![image](https://user-images.githubusercontent.com/56810348/151112925-34634298-3f57-446e-b9e8-33be610905d0.png)](https://user-images.githubusercontent.com/56810348/151112925-34634298-3f57-446e-b9e8-33be610905d0.png)


- 로그인 체크를 위해 저렇게 AuthLoginInterceptor 를 만들어주었다.

**AuthLoginInterceptor**

```java

package com.ym.cmmn;


import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

public class AuthLoginInterceptor extends HandlerInterceptorAdapter{
	
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		
		HttpSession session = request.getSession();
		
		Object obj = session.getAttribute("userMap");
		
        if (obj == null){
            response.sendRedirect("/user/signInPage");
            return false;
        }
        
        return true;
    }
  
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        super.postHandle(request, response, handler, modelAndView);
    }     
}

```

- session에 담아둔 값이 null 이면 다시 로그인페이지로 돌아가게 되어있다.


[![image](https://user-images.githubusercontent.com/56810348/151113861-090aacf7-6f79-4bf4-a38f-33f9c44d431f.png)](https://user-images.githubusercontent.com/56810348/151113861-090aacf7-6f79-4bf4-a38f-33f9c44d431f.png)

- servlet-context.xml에도 아래 코드를 방금 생성했던 Interceptor를 위해 추가시켜준다. 

**servlet-context.xml**

```xml

<!-- 인터셉터 객체 생성 -->
	<beans:bean id="AuthLoginInterceptor" class="com.ym.cmmn.AuthLoginInterceptor"></beans:bean>
	<!-- Interceptor 설정 -->
	<interceptors>
    	<interceptor>
        	<mapping path="/menu/**"/>
        	<exclude-mapping path="/user/**"/>         
        	<exclude-mapping path="/resources/**"/>
    	    <beans:ref bean="AuthLoginInterceptor"/>
    	</interceptor>
	</interceptors>

```

- class 부분에 생성한 Interceptor 경로를 적어주면 된다.

이로써 로그인 처리기능도 완성을 했다.
<br/>
이제 금일 11시에 스케쥴을 돌려서 가장 많이 추천된 메뉴를 메인페이지에 표출해주는 기능을 작성해보겠다.
<br/>
우선 위에 있는 Interceptor 같이 class와 servlet-context.xml 을 수정해줘야 한다.

[![image](https://user-images.githubusercontent.com/56810348/151114473-306b42a4-800f-40e8-80be-44f264ee5e4a.png)](https://user-images.githubusercontent.com/56810348/151114473-306b42a4-800f-40e8-80be-44f264ee5e4a.png)

- 이와같이 생성을 한뒤에

```sql

CREATE TABLE `tb_today_rstrnt` (
  `rec_date` date NOT NULL COMMENT '등록날짜',
  `rstrnt_id` varchar(100) NOT NULL COMMENT '메뉴ID',
  PRIMARY KEY (`rec_date`)
);

```

- 먼저 금일 추천결과를 등록할 테이블을 생성을 해주고

**servlet-context.xml**

```xml

<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:task="http://www.springframework.org/schema/task"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd">

-----------------------------------------------------------------------------

<task:scheduler id="TodayMenuVoteScheduler" pool-size="10" />
<task:annotation-driven scheduler="TodayMenuVoteScheduler"/>

```

- 아래 사진처럼 붙여준다.

[![image](https://user-images.githubusercontent.com/56810348/151124950-d36ebc12-7a83-43c7-87ce-ef510a4a4f9f.png)](https://user-images.githubusercontent.com/56810348/151124950-d36ebc12-7a83-43c7-87ce-ef510a4a4f9f.png)


**TodayMenuVoteScheduler**
```java

package com.ym.cmmn;

import javax.inject.Inject;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import com.ym.menu.service.MenuService;

@Component
public class TodayMenuVoteScheduler {
	
	@Inject
	MenuService menuService;
	
	//매일 11시 추천마감
	@Scheduled(cron = " 0 0 11 * * *")
	public void TodayMenuVote() throws Exception {
		menuService.todayRstrntInsert();
	}
}

```

- 11시에 스케줄을 돌릴 TodayMenuVoteScheduler를 작성해준다.

**MenuService**

```java

package com.ym.menu.service;

import java.util.Map;

public interface MenuService {

	public Map<String, Object> getMenuList() throws Exception;
	
	public Map<String, Object> menuInsertPost(Map<String, Object> requestMap) throws Exception;
	
	public Map<String, Object> menuRecPost(Map<String, Object> requestMap) throws Exception;
	
	public Map<String, Object> getVoteMenuList() throws Exception;
	
	public Map<String, Object> todayRstrntInsert() throws Exception;
}

```

- **todayRstrntInsert()** 를 추가했다.

**MenuServiceImpl**

```java

package com.ym.menu.service.impl;

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Random;

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
		int checkTodayVote = menuDAO.todayVoteMenuCheck();
		
		if(checkTodayRec == 0) {
			if(checkTodayVote == 0) {
				if(menuDAO.menuRecPost(requestMap) <= 0) {
					resultMap.put("rst_cd", "001");
					resultMap.put("rst_msg", "시스템 오류로 추천에 실패하였습니다.");
				} else {
					resultMap.put("rst_cd", "000");
					resultMap.put("rst_msg", "메뉴추천이 되었습니다.");
				}
			} else {
				resultMap.put("rst_cd", "001");
				resultMap.put("rst_msg", "금일 추천시간이 지났습니다.");
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
	
	@Override
	public Map<String, Object> todayRstrntInsert() throws Exception {
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		
		if(menuDAO.checkTodayRstrnt() == 0) {
			
			List<Map<String, Object>> resultList = menuDAO.voteMaxRstrnt();
			
			if(resultList != null && resultList.size() != 0) {
				if(resultList.size() == 1) {
					if(menuDAO.todayRstrntInsert(resultList.get(0)) <= 0) {
						resultMap.put("rst_cd", "003");
						resultMap.put("rst_msg", "시스템 오류!");
					} else {
						resultMap.put("rst_cd", "000");
						resultMap.put("rst_msg", "금일 추천결과 정상 등록!");
					}
				} else {
					Random random = new Random();
					int ran_no = random.nextInt(resultList.size());
					
					if(menuDAO.todayRstrntInsert(resultList.get(ran_no)) <= 0) {
						resultMap.put("rst_cd", "003");
						resultMap.put("rst_msg", "시스템 오류!");
					} else {
						resultMap.put("rst_cd", "000");
						resultMap.put("rst_msg", "금일 추천결과 정상 등록!");
					}
				}
			} else {
				resultMap.put("rst_cd", "002");
				resultMap.put("rst_msg", "조회결과가 없습니다.");
			}
			
		} else {
			resultMap.put("rst_cd", "001");
			resultMap.put("rst_msg", "금일 추천결과가 이미 등록되었습니다.");
		}
		
		return resultMap;
	}
}

```

- **MenuserviceImpl** 쪽에 **todayRstrntInsert()** 추가되었고 **menuRecPost()** 코드가 변경되었다.

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
	
	public int checkTodayRstrnt() throws Exception {
		return sqlSession.selectOne("com.ym.menu.dao.MenuDAO.checkTodayRstrnt");
	}
	
	public List<Map<String, Object>> voteMaxRstrnt() throws Exception {
		return sqlSession.selectList("com.ym.menu.dao.MenuDAO.voteMaxRstrnt");
	}
	
	public int todayRstrntInsert(Map<String, Object> requestMap) throws Exception {
		return sqlSession.insert("com.ym.menu.dao.MenuDAO.todayRstrntInsert", requestMap);
	}
	
	public int todayVoteMenuCheck() throws Exception {
		return sqlSession.selectOne("com.ym.menu.dao.MenuDAO.todayVoteMenuCheck");
	}
}

```

- 음..다양한 기능들이 추가되었다.

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
				NOW(),
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
	
	<select id="checkTodayRstrnt" resultType="int">
		SELECT
			COUNT(rec_date)
		FROM tb_today_rstrnt
		WHERE rec_date = CURDATE()
	</select>
	
	<select id="voteMaxRstrnt" resultType="map">
		SELECT rec_id AS rstrnt_id,
			   COUNT(*) AS vote_cnt
		FROM tb_menu_rec
		WHERE rec_date = CURDATE()
		GROUP BY rec_id	   
		HAVING COUNT(*) = 
						(
							SELECT MAX(a.vote_cnt)
							FROM 
								(
									SELECT rec_id,
										   COUNT(rec_id) AS vote_cnt
									FROM tb_menu_rec
									WHERE rec_date = CURDATE()
									GROUP BY rec_id 	   
								) a
						)
	</select>
	
	<insert id="todayRstrntInsert" parameterType="map">
		INSERT INTO 
			tb_today_rstrnt
			(
				rec_date,
				rstrnt_id
			)
		VALUES
			(
				NOW(),
				#{rstrnt_id}
			)
	</insert>
	
	<select id="todayVoteMenuCheck" resultType="int">
		SELECT COUNT(*)
		FROM tb_today_rstrnt
		WHERE rec_date = CURDATE()
	</select>
	
</mapper>

```

- 좀 많은것이 추가되었긴 하지만 천천히 복붙하고 코드 따라가면서 이해하면 좋을 것같다.

드디어 Main쪽도 작성할 때가 왔다.

[![image](https://user-images.githubusercontent.com/56810348/151119325-60d4b8be-ee11-4956-ad73-d46b71a41fa9.png)](https://user-images.githubusercontent.com/56810348/151119325-60d4b8be-ee11-4956-ad73-d46b71a41fa9.png)

- 일단 이런식으로 구성되어있다.


**MainController**

```java

package com.ym.main.controller;

import java.util.Map;

import javax.inject.Inject;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import com.ym.main.service.MainService;

@Controller
public class MainController {
	
	@Inject
	MainService MainService;

	@SuppressWarnings("unchecked")
	@GetMapping(value="/")
	public String MainPage(HttpServletRequest request, Model model) throws Exception {
		
		HttpSession session = request.getSession();
		Map<String, Object> userMap = (Map<String, Object>) session.getAttribute("userMap");
		
		model.addAttribute("userMap", userMap);
		
		return "main";
	}
	
	@PostMapping("/recMenuPost")
	@ResponseBody
	public Map<String, Object> recMenuPost() throws Exception {
		return MainService.recMenuPost();
	}
}

```

- recMenuPost 라는 코드가 추가되었다. Service 단 따라가면서 어떤기능인지 차근차근 살펴보면 좋겟다.


**MainService**

```java

package com.ym.main.service;

import java.util.Map;

public interface MainService {

	public Map<String, Object> recMenuPost() throws Exception;
}

```

**MainServiceImpl**

```java

package com.ym.main.service.impl;

import java.util.Map;

import javax.inject.Inject;

import org.springframework.stereotype.Service;

import com.ym.main.dao.MainDAO;
import com.ym.main.service.MainService;

@Service
public class MainServiceImpl implements MainService{

	@Inject
	MainDAO mainDAO;
	
	@Override
	public Map<String, Object> recMenuPost() throws Exception {
		return mainDAO.recMenuPost();
	}
}

```

**MainDAO**

```java

package com.ym.main.dao;

import java.util.Map;

import javax.inject.Inject;

import org.apache.ibatis.session.SqlSession;
import org.springframework.stereotype.Repository;

@Repository
public class MainDAO {
	
	@Inject
	SqlSession sqlSession;

	public Map<String ,Object> recMenuPost() throws Exception {
		return sqlSession.selectOne("com.ym.main.dao.MainDAO.recMenuPost");
	}
}

```

**Main_SQL.xml**

```xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ym.main.dao.MainDAO">

	<select id="recMenuPost" resultType="map">
		SELECT tr.rstrnt_nm,
			   tr.rstrnt_cl,
			   tr.rstrnt_lc 
		FROM tb_today_rstrnt ttr
		INNER JOIN tb_rstrnt tr
		ON ttr.rstrnt_id = tr.rstrnt_id
		WHERE ttr.rec_date = CURDATE() 
	</select>
</mapper>

```

이렇게 Back 단 소스가 끝이났다. 간단하게 금일 추천결과 테이블에서 결과를 뽑아와서 main.jsp에 뿌려주는 그런 기능이다.


[![image](https://user-images.githubusercontent.com/56810348/151120011-80299458-6ef1-4785-93e2-9bbbec104834.png)](https://user-images.githubusercontent.com/56810348/151120011-80299458-6ef1-4785-93e2-9bbbec104834.png)

- main.jsp 위치이다.

**main.jsp**

```html

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
	<%@ include file="/WEB-INF/include/header.jsp" %>
	
	<script>
    	$( document ).ready(function() {
    		$('body').css('background','url("/image_path/donggas.jpg") no-repeat center center fixed');
    		
    		mainRstrnt();
		});
    	
    	function mainRstrnt() {
    		$.ajax({
				type:"POST",
				url:"/recMenuPost",
				success:function(data) {
					if(data != null && data != "") {
						$("#todayRstrnt").text("오늘의 메뉴");
						
						$("#rstrnt_nm").text(data.rstrnt_nm);
						$("#rstrnt_cl").text(data.rstrnt_cl);
						$("#rstrnt_lc").text(data.rstrnt_lc);	
					} else {
						$("#todayRstrnt").text("추천된 식당이 없습니다.");
					}
				},
				error:function() {
					alert("통신오류");
				}
			});
    	}
    	
	</script>
    
    
	<!-- Navigation-->
	<%@ include file="/WEB-INF/include/navbar.jsp" %>
	<!-- Page Content-->
	<section>
    	<div class="container px-4 px-lg-5">
        	<div class="row gx-4 gx-lg-5">
            	<div class="col-lg-6">
                	<h1 class="mt-5" id="todayRstrnt"></h1>
                	<p id="rstrnt_nm"></p>
                	<p id="rstrnt_cl"></p>
                    <p id="rstrnt_lc"></p>
				</div>
			</div>
		</div>
	</section>
    
    <%@ include file="/WEB-INF/include/footer.jsp" %>

```

[![image](https://user-images.githubusercontent.com/56810348/151125151-61bfda23-e622-4d1e-b14b-d60cbba8d656.png)](https://user-images.githubusercontent.com/56810348/151125151-61bfda23-e622-4d1e-b14b-d60cbba8d656.png)

- 이렇게 추천결과가 집계가 되면 사진과 같이 나온다.

<br/>
<br/>
<br/>
<br/>
<br/>

음... 부족한 곳이 있다면 다른 곳에서 검색해서 찾아서 더 보완하면 좋을 것같아요.<br/>
또 시간이 남는 때가 온다면 더 작성해보겠습니다!<br/>

끝!