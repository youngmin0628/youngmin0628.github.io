---
title:  "Spring Boot - 웹프로젝트 제작(1)"

categories:
  - Spring Boot

toc: true
toc_sticky: true
 
date: 2022-02-09
last_modified_at: 2022-02-09
---


음...이번엔 잡다한 기능들이 들어간 웹프로젝트를 제작해보려한다. <br/>
이번것도 완성이 되고나면 그다음엔 react나 vue를 사용하여 작성을 해보겠다.

[sts 홈페이지 링크](https://spring.io/tools)

[![image](https://user-images.githubusercontent.com/56810348/153129929-63485191-0663-4cb4-821c-884537471c73.png)](https://user-images.githubusercontent.com/56810348/153129929-63485191-0663-4cb4-821c-884537471c73.png)

- 자신의 os 버전의 맞춰 다운로드를 받아 반디집으로 압축을 풀어준다.(알집으로 풀었을 때 버벅거림이 있음을 봐서)


[![image](https://user-images.githubusercontent.com/56810348/153130943-bbec18c3-f3f5-4088-96c0-e194b7415c5f.png)](https://user-images.githubusercontent.com/56810348/153130943-bbec18c3-f3f5-4088-96c0-e194b7415c5f.png)

- 저는 D 드라이브에 풀었다.


[![image](https://user-images.githubusercontent.com/56810348/153131043-a235f725-3db5-4efa-a2b2-43d8e259e1e1.png)](https://user-images.githubusercontent.com/56810348/153131043-a235f725-3db5-4efa-a2b2-43d8e259e1e1.png)

- 안에 들어가면 content 라고 있는데 이것도 풀어준다.


[![image](https://user-images.githubusercontent.com/56810348/153131449-25628f61-0779-4fa5-8a95-19825ae2ce8f.png)](https://user-images.githubusercontent.com/56810348/153131449-25628f61-0779-4fa5-8a95-19825ae2ce8f.png)

- 쭈욱 안으로 들어가면 위에거가 실행파일 아래거가 ini설정파일이다.(위에거 눌러서 실행)


[![image](https://user-images.githubusercontent.com/56810348/153131752-41164834-86c1-493f-97c4-087fdbb3dc83.png)](https://user-images.githubusercontent.com/56810348/153131752-41164834-86c1-493f-97c4-087fdbb3dc83.png)

- 잘 실행되는군요.


[![image](https://user-images.githubusercontent.com/56810348/153131882-cfeace1f-2e64-4c8d-8504-10de1c8a7648.png)](https://user-images.githubusercontent.com/56810348/153131882-cfeace1f-2e64-4c8d-8504-10de1c8a7648.png)

- 왼쪽 상단 메뉴 File > New > Spring Starter Project 를 클릭


[![image](https://user-images.githubusercontent.com/56810348/153132508-06079e6a-7d7c-4b79-8578-afa49c424a41.png)](https://user-images.githubusercontent.com/56810348/153132508-06079e6a-7d7c-4b79-8578-afa49c424a41.png)

- 설정해 줄곳 = 빨간 네모칸
- 다르게 해도 상관없긴하지만 이 글을 보고 쭉 따라할거면 Type, Java version, Packaging, Launguage는 사진과 똑같이 해주면된다.


[![image](https://user-images.githubusercontent.com/56810348/153132863-b0ce2425-fab5-4d7a-856d-5bba4d03906c.png)](https://user-images.githubusercontent.com/56810348/153132863-b0ce2425-fab5-4d7a-856d-5bba4d03906c.png)

- 찾아서 추가시켜주고 Finish 클릭


[![image](https://user-images.githubusercontent.com/56810348/153132949-8426c976-c86a-4b07-9aa6-dbd0eeb58edf.png)](https://user-images.githubusercontent.com/56810348/153132949-8426c976-c86a-4b07-9aa6-dbd0eeb58edf.png)

- 빙글빙글 돌면서 열심히 설치 중이니 기다린다.


[![image](https://user-images.githubusercontent.com/56810348/153134934-d19dbda1-b0d9-4362-8244-942ee5162e3e.png)](https://user-images.githubusercontent.com/56810348/153134934-d19dbda1-b0d9-4362-8244-942ee5162e3e.png)

- 상단 메뉴에 Help > Eclipse MarketPlace 클릭


[![image](https://user-images.githubusercontent.com/56810348/153139044-0d8b2cb8-52c5-4fa0-953c-df85d75750db.png)](https://user-images.githubusercontent.com/56810348/153139044-0d8b2cb8-52c5-4fa0-953c-df85d75750db.png)

- java and web을 검색한 후 빨간 네모친 부분 install 해준다.


[![image](https://user-images.githubusercontent.com/56810348/153143848-3c2596ac-5f2a-4c30-ae40-5f5ab7dc430c.png)](https://user-images.githubusercontent.com/56810348/153143848-3c2596ac-5f2a-4c30-ae40-5f5ab7dc430c.png)

- ctrl+shift+r 키를 눌러 파일명 검색해서 수정해본다 아래와같이.

**application.properties**

```properties

server.port=8082
spring.mvc.view.prefix=/WEB-INF/
spring.mvc.view.suffix=.jsp

```

**pom.xml**

```xml
        <!-- 추가 -->
        <dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.tomcat.embed</groupId>
			<artifactId>tomcat-embed-jasper</artifactId>
		</dependency>

```

**TestController.java**
```java

package com.song.bootPjt;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class TestController {

	@GetMapping("/")
	public String test() throws Exception {
		return "/test";
	}
}

```

[![image](https://user-images.githubusercontent.com/56810348/153154686-17768780-2fa5-4e97-b44f-4c179f6b4efc.png)](https://user-images.githubusercontent.com/56810348/153154686-17768780-2fa5-4e97-b44f-4c179f6b4efc.png)

- 이렇게 똑같이 만들어본다. 잘 나오나 test 할려고..


[![image](https://user-images.githubusercontent.com/56810348/153154910-bb1c0359-8ca6-42e3-a0ea-5b4a2df7b56a.png)](https://user-images.githubusercontent.com/56810348/153154910-bb1c0359-8ca6-42e3-a0ea-5b4a2df7b56a.png)

- 실행 후 localhost:%server.port설정한값% 구동시켜주면 잘 나온거같다.