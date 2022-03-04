---
title:  "Spring Boot - 웹프로젝트 제작(2) - DB연결까지"

categories:
  - Spring Boot

toc: true
toc_sticky: true
 
date: 2022-03-02
last_modified_at: 2022-03-02
---


무엇을 만들지 고민하다가 로또 추첨결과를 출력하는 것을 만들어보려한다.
페이지는 하나로만 갈 생각이다.

우선 TestController와 test.jsp를 삭제해준다.

[부트스트랩 테마 다운로드 링크](https://startbootstrap.com/theme/sb-admin-2)

[![image](https://user-images.githubusercontent.com/56810348/156322364-e1bd10bd-3fc6-4796-9bcf-904c929b9788.png)](https://user-images.githubusercontent.com/56810348/156322364-e1bd10bd-3fc6-4796-9bcf-904c929b9788.png)

- 압축을 푼뒤 사진과 같은 폴더안에 넣어준다.

**pom.xml**

```xml

        <dependency>
		    <groupId>org.mybatis.spring.boot</groupId>
		    <artifactId>mybatis-spring-boot-starter</artifactId>
		    <version>1.3.2</version>
		</dependency>
		
		<dependency>
		    <groupId>mysql</groupId>
		    <artifactId>mysql-connector-java</artifactId>
		    <scope>runtime</scope>
		</dependency>

```

- pom.xml에 mybatis와 mysql connector 를 추가시켜준다.


**application.properties**

```properties

mybatis.mapper-locations:classpath:mapper/**/*.xml

spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/데이터베이스명?useUnicode=yes&characterEncoding=UTF-8&allowMultiQueries=true&serverTimezone=Asia/Seoul
spring.datasource.username=ID입력
spring.datasource.password=PW입력

```

- application.properties에 추가시켜준다.


[![image](https://user-images.githubusercontent.com/56810348/156322889-f5f4e3ff-9b25-4fb7-bf86-fd9d80b28d0e.png)](https://user-images.githubusercontent.com/56810348/156322889-f5f4e3ff-9b25-4fb7-bf86-fd9d80b28d0e.png)

[![image](https://user-images.githubusercontent.com/56810348/156323163-fe83526d-0d99-448f-89a2-5613c0a19be8.png)](https://user-images.githubusercontent.com/56810348/156323163-fe83526d-0d99-448f-89a2-5613c0a19be8.png)

- 사진과 같이 com.song.bootPjt 안에 main 패키지를 생성하고 controller, service, impl, mapper 패키지를 추가시켜준다.


**MainController**

```java

package com.song.bootPjt.main.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import com.song.bootPjt.main.service.MainService;

@Controller
public class MainController {
	
	@Autowired
	MainService mainService;

	@GetMapping("/")
	public String getMainPage() throws Exception {
		
		System.out.println(mainService.getLottoList());
		
		return "/main/main";
	}
}

```

**MainService**

```java

package com.song.bootPjt.main.service;

import java.util.Map;

public interface MainService {

	public Map<String, Object> getLottoList() throws Exception;
}

```

**MainServiceImpl**

```java

package com.song.bootPjt.main.service.impl;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.song.bootPjt.main.mapper.MainMapper;
import com.song.bootPjt.main.service.MainService;

@Service("mainService")
public class MainServiceImpl implements MainService{

	@Autowired
	MainMapper mainMapper;
	
	@Override
	public Map<String, Object> getLottoList() throws Exception {
		
		List<Map<String, Object>> resultList = mainMapper.getLottoList();
		
		Map<String ,Object> resultMap = new HashMap<String, Object>();
		
		resultMap.put("data", resultList);
		
		return resultMap;
		
	}
}

```

**MainMapper**

```java

package com.song.bootPjt.main.mapper;

import java.util.List;
import java.util.Map;

import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface MainMapper {

	List<Map<String, Object>> getLottoList() throws Exception;
}

```

- 여기까지 잘 따라왔다면 이제 jsp와 xml를 만들어준다.
- xml 부터 만들어보겠다.


[![image](https://user-images.githubusercontent.com/56810348/156323859-1c2fc5c9-95c5-4428-a0c3-8078a2ff0f5c.png)](https://user-images.githubusercontent.com/56810348/156323859-1c2fc5c9-95c5-4428-a0c3-8078a2ff0f5c.png)

- 똑같은 경로에 똑같이 생성을 해준다.

**Main_SQL.xml**

```sql

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.song.bootPjt.main.mapper.MainMapper">
	
	<select id="getLottoList" resultType="map">
		SELECT *
		FROM tb_lotto_list
	</select>
</mapper>

```

**main.jsp**

```jsp

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>

<head>

	<meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">
	
	<title>로또 뿌려주는 페이지</title>
	
	<!-- Custom fonts for this template-->
    <link href="vendor/fontawesome-free/css/all.min.css" rel="stylesheet" type="text/css">
    <link
        href="https://fonts.googleapis.com/css?family=Nunito:200,200i,300,300i,400,400i,600,600i,700,700i,800,800i,900,900i"
        rel="stylesheet">

    <!-- Custom styles for this template-->
    <link href="css/sb-admin-2.min.css" rel="stylesheet">
</head>

<body id="page-top">
    <!-- Page Wrapper -->
    <div id="wrapper">
    
        <!-- Content Wrapper -->
        <div id="content-wrapper" class="d-flex flex-column">

			<!-- Main Content -->
        	<div id="content">

                <!-- Topbar -->
                <nav class="navbar navbar-expand navbar-light bg-white topbar mb-4 static-top shadow">

                    <!-- Sidebar Toggle (Topbar) -->
                    <button id="sidebarToggleTop" class="btn btn-link d-md-none rounded-circle mr-3">
                        <i class="fa fa-bars"></i>
                    </button>

                    <!-- Topbar Search 
                    <form class="d-none d-sm-inline-block form-inline mr-auto ml-md-3 my-2 my-md-0 mw-100 navbar-search">
                        <div class="input-group">
                            <input type="text" class="form-control bg-light border-0 small" placeholder="Search for..." aria-label="Search" aria-describedby="basic-addon2">
                            <div class="input-group-append">
                                <button class="btn btn-primary" type="button">
                                    <i class="fas fa-search fa-sm"></i>
                                </button>
                            </div>
                        </div>
                    </form>
                    -->
                    
	                <!-- Topbar Navbar -->
					<ul class="navbar-nav ml-auto"></ul>
					
                </nav>
                <!-- End of Topbar -->

                <!-- Begin Page Content -->
                <div class="container-fluid">

                    <!-- Page Heading -->
                    <div class="d-sm-flex align-items-center justify-content-between mb-4">
                        <h1 class="h3 mb-0 text-gray-800">로또당첨기원</h1>
                        <a href="#" class="d-none d-sm-inline-block btn btn-sm btn-primary shadow-sm">
                        <i class="fas fa-download fa-sm text-white-50"></i> 
                        	금주 추천 로또번호
						</a>
                    </div>

                    <!-- Content Row -->
                    <div class="row">

                        <!-- 최근 추첨일 -->
                        <div class="col-xl-3 col-md-6 mb-4">
                            <div class="card border-left-primary shadow h-100 py-2">
                                <div class="card-body">
                                    <div class="row no-gutters align-items-center">
                                        <div class="col mr-2">
                                            <div class="text-xs font-weight-bold text-primary text-uppercase mb-1">
                                                최근 추첨일
											</div>
                                            <div class="h5 mb-0 font-weight-bold text-gray-800">
                                            	2022-03-01
											</div>
                                        </div>
                                        <div class="col-auto">
                                            <i class="fas fa-calendar fa-2x text-gray-300"></i>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- 최근 추첨 총 상금액 -->
                        <div class="col-xl-3 col-md-6 mb-4">
                            <div class="card border-left-success shadow h-100 py-2">
                                <div class="card-body">
                                    <div class="row no-gutters align-items-center">
                                        <div class="col mr-2">
                                            <div class="text-xs font-weight-bold text-success text-uppercase mb-1">
                                                최근 추첨 총 상금액
											</div>
                                            <div class="h5 mb-0 font-weight-bold text-gray-800">
                                            	100020200 원
                                            </div>
                                        </div>
                                        <div class="col-auto">
                                            <i class="fas fa-won-sign fa-2x text-gray-300"></i>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- 최근 추첨 1등 상금액 -->
                        <div class="col-xl-3 col-md-6 mb-4">
                            <div class="card border-left-info shadow h-100 py-2">
                                <div class="card-body">
                                    <div class="row no-gutters align-items-center">
                                        <div class="col mr-2">
                                            <div class="text-xs font-weight-bold text-info text-uppercase mb-1">
                                            	최근 추첨 1등 상금액
                                            </div>
                                            <div class="h5 mb-0 font-weight-bold text-gray-800">
                                            	5020200 원
                                            </div>
                                        </div>
										<div class="col-auto">
                                            <i class="fas fa-won-sign fa-2x text-gray-300"></i>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- 최근 추첨 당첨수 -->
                        <div class="col-xl-3 col-md-6 mb-4">
                            <div class="card border-left-warning shadow h-100 py-2">
                                <div class="card-body">
                                    <div class="row no-gutters align-items-center">
                                        <div class="col mr-2">
                                            <div class="text-xs font-weight-bold text-warning text-uppercase mb-1">
                                                최근 추첨 당첨수
											</div>
                                            <div class="h5 mb-0 font-weight-bold text-gray-800">
                                            	18 명
                                            </div>
                                        </div>
                                        <div class="col-auto">
                                            <i class="fas fa-comments fa-2x text-gray-300"></i>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Content Row -->

                    <div class="row">

                        <!-- Area Chart -->
                        <div class="col-xl-12 col-lg-12">
                            <div class="card shadow mb-4">
                                <!-- Card Header - Dropdown -->
                                <div
                                    class="card-header py-3 d-flex flex-row align-items-center justify-content-between">
                                    <h6 class="m-0 font-weight-bold text-primary">Earnings Overview</h6>
                                    <div class="dropdown no-arrow">
                                        <a class="dropdown-toggle" href="#" role="button" id="dropdownMenuLink"
                                            data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                                            <i class="fas fa-ellipsis-v fa-sm fa-fw text-gray-400"></i>
                                        </a>
                                        <div class="dropdown-menu dropdown-menu-right shadow animated--fade-in"
                                            aria-labelledby="dropdownMenuLink">
                                            <div class="dropdown-header">Dropdown Header:</div>
                                            <a class="dropdown-item" href="#">Action</a>
                                            <a class="dropdown-item" href="#">Another action</a>
                                            <div class="dropdown-divider"></div>
                                            <a class="dropdown-item" href="#">Something else here</a>
                                        </div>
                                    </div>
                                </div>
                                <!-- Card Body -->
                                <div class="card-body">
                                    <div class="chart-area">
                                        <canvas id="myAreaChart"></canvas>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Content Row -->
                    <div class="row">

                        <!-- Content Column -->
                        <div class="col-lg-12 mb-12">

                            <!-- Color System -->
                            <div class="row">
                                <div class="col-lg-6 mb-4">
                                    <div class="card bg-primary text-white shadow">
                                        <div class="card-body">
                                            첫번째 번호
                                            <div class="text-white-50 small">
	                                            12
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-lg-6 mb-4">
                                    <div class="card bg-primary text-white shadow">
                                        <div class="card-body">
                                            두번째 번호
                                            <div class="text-white-50 small">
	                                            12
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-lg-6 mb-4">
                                    <div class="card bg-primary text-white shadow">
                                        <div class="card-body">
                                            세번째 번호
                                            <div class="text-white-50 small">
	                                            12
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-lg-6 mb-4">
                                    <div class="card bg-primary text-white shadow">
                                        <div class="card-body">
                                            네번째 번호
                                            <div class="text-white-50 small">
	                                            12
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-lg-6 mb-4">
                                    <div class="card bg-primary text-white shadow">
                                        <div class="card-body">
                                            다섯번째 번호
                                            <div class="text-white-50 small">
	                                            12
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-lg-6 mb-4">
                                    <div class="card bg-primary text-white shadow">
                                        <div class="card-body">
                                            여섯번째 번호
                                            <div class="text-white-50 small">
	                                            12
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-lg-6 mb-4">
                                    <div class="card bg-danger text-white shadow">
                                        <div class="card-body">
                                            보너스 번호
                                            <div class="text-white-50 small">
                                            	12
											</div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                </div>
                <!-- /.container-fluid -->

            </div>
            <!-- End of Main Content -->

            <!-- Footer -->
            <footer class="sticky-footer bg-white">
                <div class="container my-auto">
                    <div class="copyright text-center my-auto">
                        <span>Copyright &copy; Your Website 2021</span>
                    </div>
                </div>
            </footer>
            <!-- End of Footer -->

        </div>
        <!-- End of Content Wrapper -->

    </div>
    <!-- End of Page Wrapper -->

    <!-- Scroll to Top Button-->
    <a class="scroll-to-top rounded" href="#page-top">
        <i class="fas fa-angle-up"></i>
    </a>

    <!-- Logout Modal-->
    <div class="modal fade" id="logoutModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel"
        aria-hidden="true">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">Ready to Leave?</h5>
                    <button class="close" type="button" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">×</span>
                    </button>
                </div>
                <div class="modal-body">Select "Logout" below if you are ready to end your current session.</div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" type="button" data-dismiss="modal">Cancel</button>
                    <a class="btn btn-primary" href="login.html">Logout</a>
                </div>
            </div>
        </div>
    </div>

	<!-- Bootstrap core JavaScript-->
    <script src="vendor/jquery/jquery.min.js"></script>
    <script src="vendor/bootstrap/js/bootstrap.bundle.min.js"></script>

    <!-- Core plugin JavaScript-->
    <script src="vendor/jquery-easing/jquery.easing.min.js"></script>

    <!-- Custom scripts for all pages-->
    <script src="js/sb-admin-2.min.js"></script>

    <!-- Page level plugins -->
    <script src="vendor/chart.js/Chart.min.js"></script>

    <!-- Page level custom scripts -->
    <script src="js/demo/chart-area-demo.js"></script>
    <script src="js/demo/chart-pie-demo.js"></script>
    
</body>

</html>

```

- 여기까지 잘 따라왔다면 이제 테이블만 만들어주면 끝난다.

[![image](https://user-images.githubusercontent.com/56810348/156324326-932e09a2-6197-4168-907b-807222476b54.png)](https://user-images.githubusercontent.com/56810348/156324326-932e09a2-6197-4168-907b-807222476b54.png)

- lotto 데이터베이스 안에 tb_lotto_list 라는 테이블을 생성해주었다. 
- DB는 MySql을 사용하였다.

```sql

CREATE TABLE `tb_lotto_list` (
  `result_dt` date NOT NULL COMMENT '추첨일',
  `result_round` int DEFAULT NULL COMMENT '추첨 회차',
  `lottery_no1` int DEFAULT NULL COMMENT '첫번째 추첨번호',
  `lottery_no2` int DEFAULT NULL COMMENT '두번째 추첨번호',
  `lottery_no3` int DEFAULT NULL COMMENT '세번째 추첨번호',
  `lottery_no4` int DEFAULT NULL COMMENT '네번째 추첨번호',
  `lottery_no5` int DEFAULT NULL COMMENT '다섯번째 추첨번호',
  `lottery_no6` int DEFAULT NULL COMMENT '여섯번째 추첨번호',
  `lottery_bonus_no` int DEFAULT NULL COMMENT '보너스 추첨번호',
  `total_money` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '총 상금액',
  `first_money` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '1등 상금액',
  `first_people` varchar(10) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '1등 당첨인원',
  PRIMARY KEY (`result_dt`)
)

```


[![image](https://user-images.githubusercontent.com/56810348/156324591-59bd8b6e-1eb5-4a0b-a271-13bc3c76e985.png)](https://user-images.githubusercontent.com/56810348/156324591-59bd8b6e-1eb5-4a0b-a271-13bc3c76e985.png)

- 이렇게 페이지가 보여진다면 이상없이 마무리되었다.

다음 글에서는 로또 API로 데이터를 받아와 테이블에 INSERT 한 후 main.jsp에 뿌려주는 작업을 해보도록하겠다. 지금까지는 시작준비를 다 갖췄다고 생각하면 되겠다.