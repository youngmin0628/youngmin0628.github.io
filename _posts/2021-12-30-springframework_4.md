---
title:  "Spring Framework(4) - 시작 전 마지막 셋팅(DB연결, pom.xml, web.xml)"

categories:
  - Spring Framework

toc: true
toc_sticky: true
 
date: 2021-12-30
last_modified_at: 2021-12-30
---

<h1>시작 전 마지막 셋팅(DB연결, pom.xml, web.xml)</h1>


<h2>1. DB 설치(MySQL)</h2>

[MySQL 다운로드](https://dev.mysql.com/downloads/installer/)

- 딱히 건들지말고 쭈욱 설치해준다.(Next만 열심히)


![image](https://user-images.githubusercontent.com/56810348/147713380-455158dc-6866-4a90-8675-f882eb28733e.png)


![image](https://user-images.githubusercontent.com/56810348/147713439-93b6474b-06e9-4385-8a49-945203e3b89d.png)


![image](https://user-images.githubusercontent.com/56810348/147713502-d38b833f-00fe-4c5b-b8eb-16cb34873616.png)


![image](https://user-images.githubusercontent.com/56810348/147713523-d9d730f5-328d-48d2-bc91-801168d4abd8.png)


![image](https://user-images.githubusercontent.com/56810348/147713741-3d2f8718-3d21-4955-95c3-24dc294c63fb.png)


![image](https://user-images.githubusercontent.com/56810348/147713769-9a1c7d32-136e-44b2-95c0-e5c46857fcb9.png)

- root 계정에 대한 패스워드이며 까먹지않게 패스워드를 설정해준다.


![image](https://user-images.githubusercontent.com/56810348/147713858-98506db8-7f35-4bb5-9e60-c72d3e3cab3b.png)

- Finish 설치완료!


<h2>2. pom.xml, web.xml 설정</h2>

![image](https://user-images.githubusercontent.com/56810348/147732106-d3ab04f1-8f2b-4f57-bd57-d43beb1c2a01.png)

- 이 부분을 이렇게 변경해준다.

``` xml

  <properties>
      <java-version>11</java-version>
      <org.springframework-version>5.2.8.RELEASE</org.springframework-version>
      <org.aspectj-version>1.9.6</org.aspectj-version>
      <org.slf4j-version>1.7.25</org.slf4j-version>
  </properties>

```

![image](https://user-images.githubusercontent.com/56810348/147733015-67a20461-496a-4d54-9be0-c2bd3b460827.png)

``` xml

- MySQL 연결을 위한 JDBC도 pom에 적어줍니다. (dependencies 안에 적어야합니다. 위치 잘 보세요.)

	<!--MySQL JDBC -->
	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<version>8.0.26</version>
	</dependency>
	
	<!-- Spring JDBC -->
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-jdbc</artifactId>
		<version>${org.springframework-version}</version>
	</dependency>
	
	<!-- Mybatis Spring -->
	<dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis-spring</artifactId>
		<version>1.3.2</version>
	</dependency>

	<!-- Mybatis -->
	<dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis</artifactId>
		<version>3.5.6</version>
	</dependency>
	
	<!-- Spring Test -->
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-test</artifactId>
		<version>${org.springframework-version}</version>
		<scope>test</scope>
	</dependency>

```


![image](https://user-images.githubusercontent.com/56810348/147733755-be31f0fa-7b5d-4ff5-9b73-16fe7ee7e3c3.png)

- junit도 4.7 에서 4.12로 업그레이드 해줍니다.

``` xml

    <!-- junit Test -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency> 

```


**DB 연결을 테스트 해보기 전에 dataabase 부터 생성해보자**

![image](https://user-images.githubusercontent.com/56810348/147734325-0c40740a-652a-44e2-b853-6767eb2e6847.png)

- 윈도우 + S 를 누른다.
- MySQL 8.0 Command Line Client를 실행시켜준다.


![image](https://user-images.githubusercontent.com/56810348/147734380-1a334355-a6a4-4628-a27c-046ca98ad876.png)

- 설치할 때 설정한 비밀번호를 입력한다.


![image](https://user-images.githubusercontent.com/56810348/147734469-dcef0c3e-6a03-4799-b8f7-9a0a780b9a49.png)

- create database 데이터베이스이름; 하고 엔터를 쳐주면 데이터베이스가 생성된다.
- 다시 이클립스로 돌아간다.(데이터베이스 이름을 기억해두자)
- 생각이 안난다면 show databases; 를 입력해준다.


![image](https://user-images.githubusercontent.com/56810348/147734669-fac1f691-156c-4736-8aa8-c67c59607216.png)

- 이곳이 DB정보를 적는 곳이다.


![image](https://user-images.githubusercontent.com/56810348/147734805-354878db-ddf0-44f0-b7af-4899d8ecaee4.png)

- 하단 Namespaces 탭에 **aop**, **beans**, **context**, **jdbc**, **mybatis-spring** 을 체크해준다.


![image](https://user-images.githubusercontent.com/56810348/147734977-37e62478-6c8d-49d9-8ef3-41777c3b86c8.png)

- 무언가 잔뜩생겼다. 성공이다.


```xml

  <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
      <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></property>
      <property name="url" value="jdbc:mysql://127.0.0.1:3306/데이터베이스 이름?useSSL=false&amp;serverTimezone=UTC"></property>
      <property name="username" value="root"></property>
      <property name="password" value="비밀번호"></property>
  </bean>

  <!-- 여기는 일단 냅두자. -->
  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="dataSource" ref="dataSource"></property>
  </bean>

```

![image](https://user-images.githubusercontent.com/56810348/147735593-dc8b0551-5416-480f-94fb-d4fd6a75de62.png)

- 아래처럼 복사해준다
- 수정을 해줘야한다. 내가 설정한 것에 맞춰서

```xml

  <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
      <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></property>
      <property name="url" value="jdbc:mysql://127.0.0.1:3306/ymtest?useSSL=false&amp;serverTimezone=UTC"></property>
      <property name="username" value="root"></property>
      <property name="password" value="1234"></property>
  </bean>

```

- password 와 아까 생성한 데이터베이스 명을 적어줬다.


![image](https://user-images.githubusercontent.com/56810348/147735908-d788396d-aa50-4fc9-93a2-4b744bd90a0c.png)

- 우클릭 > New > Junit Test Case 클릭



![image](https://user-images.githubusercontent.com/56810348/147736055-ef2d9c04-ae25-4378-86e6-da96cac64f93.png)

- DB가 정말 잘 붙었는지 테스트하기위해 만드는 것이다.


```java

package com.ym.song;

import java.sql.Connection;

import javax.inject.Inject;
import javax.sql.DataSource;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = { "file:src/main/webapp/WEB-INF/spring/**/root-context.xml" })
public class TestDBConnect {

	@Inject
	private DataSource dataSource;
	
	@Test
	public void test() {
		try  
		{
			Connection con = dataSource.getConnection();
			System.out.println("***** 연결정보 : " + con + " *****");
		} 
		catch (Exception e) 
		{
			e.printStackTrace();
		}
	}
}

```


![image](https://user-images.githubusercontent.com/56810348/147736446-cc6e9a40-0f11-487d-ac97-65ec8d7881c7.png)

- 파일 선택 후 우클릭 > Run As > Junit Test 클릭



![image](https://user-images.githubusercontent.com/56810348/147736537-ce95fd6b-58ee-413f-a617-c9bf32d9bfe9.png)

- 콘솔에 에러없이 정상적으로 출력되면 성공이다.


![image](https://user-images.githubusercontent.com/56810348/147798960-0cf77a53-c188-4c51-a683-95e6aca6cf43.png)


```xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee; https://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

	<!-- The definition of the Root Spring Container shared by all Servlets and Filters -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring/root-context.xml</param-value>
	</context-param>
	
	<!-- Creates the Spring Container shared by all Servlets and Filters -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<!-- Processes application requests -->
	<servlet>
		<servlet-name>appServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
		
	<servlet-mapping>
		<servlet-name>appServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	
	<!-- 한글설정 -->
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>
			org.springframework.web.filter.CharacterEncodingFilter
		</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
		<init-param>
			<param-name>forceEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>

	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<!-- 한글설정 -->

</web-app>

```

- web.xml에 그대로 복사해서 붙여넣어주면 web.xml 설정은 끝!