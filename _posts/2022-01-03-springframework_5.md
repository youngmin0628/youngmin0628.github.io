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

- servlet-context.xml 에서 prefix, subfix를 확인한다.
- context:component-scan baas-package 부분을 패키지 구조에 맞춰 수정한다.(com.ym.* 로 하면 하위 패키지 전부 적용)


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
- src/main/resources 하위에 위에서 붙여준 소스안에 있는 **mapperLocations** 에 경로에 맞춰서 폴더와 Menu_SQL.xml 파일을 생성해준다.