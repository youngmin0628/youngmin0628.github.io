---
title:  "Spring Framework(5) - 회사 점심 투표 웹사이트(1)"

categories:
  - Spring Framework

toc: true
toc_sticky: true
 
date: 2022-01-03
last_modified_at: 2022-01-03
---

<h1>회사 점심 투표 웹사이트(1)</h1>


[![image](https://user-images.githubusercontent.com/56810348/147907530-2fbf8014-1aa4-4f82-b9d8-2855f4781606.png)](https://user-images.githubusercontent.com/56810348/147907530-2fbf8014-1aa4-4f82-b9d8-2855f4781606.png)

- servlet-context.xml 에서 prefix, subfix를 확인
- context:component-scan baas-package 부분을 패키지 구조에 맞춰 수정(com.ym.* 로 하면 하위 패키지 전부 적용)


[![image](https://user-images.githubusercontent.com/56810348/147909264-94885797-661d-4378-92ef-24fcfe235b21.png)](https://user-images.githubusercontent.com/56810348/147909264-94885797-661d-4378-92ef-24fcfe235b21.png)

- 기존에 있던 패키지를 삭제하고 우클릭 > new > package <br/>
**com.ym.controller**, <br/>
**com.ym.service**, <br/>
**com.ym.service.impl**, <br/> 
**com.ym.dao** <br/>


[![image](https://user-images.githubusercontent.com/56810348/147909338-3d83c483-adb1-4f45-9f78-fda60ed96cfe.png)](https://user-images.githubusercontent.com/56810348/147909338-3d83c483-adb1-4f45-9f78-fda60ed96cfe.png)

- 각각 패키지에 우클릭 > new <br/>
 **MenuController(Class)**, <br/>
 **MenuDAO(Class)**, <br/> 
 **MenuService(Interface)**, <br/>
 **MenuServiceImpl(Class)** <br/>


[![image](https://user-images.githubusercontent.com/56810348/147912658-b607f956-00c4-414c-b413-223a1dec2140.png)](https://user-images.githubusercontent.com/56810348/147912658-b607f956-00c4-414c-b413-223a1dec2140.png)

```xml

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="mapperLocations" value="classpath:mappers/**/*_SQL.xml"></property>
    </bean>
	
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate" destroy-method="clearCache">
        <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"></constructor-arg>
    </bean>

```

- 예전 DB 접속정보를 설정했던 root-context에 붙여넣어준다.
- src/main/resources 하위에 위에서 붙여준 소스안에 있는 **mapperLocations** 에 경로에 맞춰서 폴더와 Menu_SQL.xml 파일을 생성

<h2>테이블 생성</h2>

[DBeaver 다운로드](https://dbeaver.io/download/)

- 회사에서 자주 쓰는 거 같아 이거로 다운받겠다.

[![image](https://user-images.githubusercontent.com/56810348/148002308-a6c790ad-e73e-4734-8442-007b6063f7b9.png)](https://user-images.githubusercontent.com/56810348/148002308-a6c790ad-e73e-4734-8442-007b6063f7b9.png)

- 쭈욱 설치해주시면 됩니다.


[![image](https://user-images.githubusercontent.com/56810348/148002444-449fbfb9-5e08-48fa-b042-e7599d0e5070.png)](https://user-images.githubusercontent.com/56810348/148002444-449fbfb9-5e08-48fa-b042-e7599d0e5070.png)


[![image](https://user-images.githubusercontent.com/56810348/148002631-cb61c9d0-4f7f-4e89-90b9-986bfdf821ab.png)](https://user-images.githubusercontent.com/56810348/148002631-cb61c9d0-4f7f-4e89-90b9-986bfdf821ab.png)

- 생성했던 데이터베이스 명을 입력
- MySQL 계정 비밀번호를 입력 후 완료클릭!


[![image](https://user-images.githubusercontent.com/56810348/148002732-b057186c-50c7-421a-987f-39abf4e07b33.png)](https://user-images.githubusercontent.com/56810348/148002732-b057186c-50c7-421a-987f-39abf4e07b33.png)

- 이제 손쉽게 DB를 만질 수 있다.



[![image](https://user-images.githubusercontent.com/56810348/148012597-bbec7a4e-5eb4-4d85-8e63-5ba76e20c5f6.png)](https://user-images.githubusercontent.com/56810348/148012597-bbec7a4e-5eb4-4d85-8e63-5ba76e20c5f6.png)

```sql

CREATE TABLE `tb_menu` (
  `menu_id` int NOT NULL AUTO_INCREMENT COMMENT '메뉴고유번호',
  `menu_nm` varchar(100) DEFAULT NULL COMMENT '메뉴이름',
  `menu_cl` varchar(100) DEFAULT NULL COMMENT '메뉴분류',
  `rstrnt_nm` varchar(100) DEFAULT NULL COMMENT '식당이름',
  `rstrnt_lc` varchar(100) DEFAULT NULL COMMENT '식당위치',
  PRIMARY KEY (`menu_id`)
)

```

- 상단 메뉴의 SQL 편집기 > 새 SQL 편집기 클릭 후 아래 SQL문을 복사한 후 실행(ctrl + Enter)


[![image](https://user-images.githubusercontent.com/56810348/148013578-c17a8303-3bb5-48c2-9ae3-350da6d600f7.png)](https://user-images.githubusercontent.com/56810348/148013578-c17a8303-3bb5-48c2-9ae3-350da6d600f7.png)

- views 폴더 아래 menu 폴더 생성 후 menuList.jsp를 생성
- home.jsp를 main.jsp로 변경