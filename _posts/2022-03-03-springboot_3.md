---
title:  "Spring Boot - 웹프로젝트 제작(3) - API 받아오기"

categories:
  - Spring Boot

toc: true
toc_sticky: true
 
date: 2022-03-03
last_modified_at: 2022-03-03
---

[![image](https://user-images.githubusercontent.com/56810348/156527874-0a96fc9a-0ca7-45bf-b0c7-82ff0a4ad085.png)](https://user-images.githubusercontent.com/56810348/156527874-0a96fc9a-0ca7-45bf-b0c7-82ff0a4ad085.png)

- 로또 당첨결과를 조회하는 API의 데이터를 받아와 테이블에 저장을 해보겠다.

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
		
		//로또 데이터 조회하여 가져오는 부분
		mainService.LottoApiGetData();
		
		//삭제System.out.println(mainService.getLottoList());
		
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
	
	public void LottoApiGetData() throws Exception;
}

```

**MainServiceImpl**

```java

package com.song.bootPjt.main.service.impl;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
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
	
	@Override
	public void LottoApiGetData() throws Exception {
		
		//최신회차 조회
		int drwNo = mainMapper.checkResultRound() + 1;

		//번호증가하며 쭉 조회해서 테이블에 데이터 쌓기
		while(true) {
			Map<String, Object> objMap= null;
			
			String string_url = "https://www.dhlottery.co.kr/common.do?method=getLottoNumber&drwNo=" + drwNo;
			
			URL url = new URL(string_url);
			
			BufferedReader bf;
			String line = "";
			String result = "";
			
			bf = new BufferedReader(new InputStreamReader(url.openStream()));
			
			while((line=bf.readLine()) != null){
				result=result.concat(line); 
				//System.out.println("받아온 데이터 찍어보기 : \n" + result);
			}
			
			JSONParser jsonParser = new JSONParser();
			JSONObject obj = (JSONObject) jsonParser.parse(result);
			
			if(obj.get("returnValue").toString().equals("success")) { // 정상 동작여부
				
				//방법 1 
				String result_dt = obj.get("drwNoDate").toString(); // 추첨일
				String result_round = obj.get("drwNo").toString();  // 추첨회차
				
				String lottery_no1 = obj.get("drwtNo1").toString(); // 추첨번호1
				String lottery_no2 = obj.get("drwtNo2").toString(); // 추첨번호2
				String lottery_no3 = obj.get("drwtNo3").toString(); // 추첨번호3
				String lottery_no4 = obj.get("drwtNo4").toString(); // 추첨번호4
				String lottery_no5 = obj.get("drwtNo5").toString(); // 추첨번호5
				String lottery_no6 = obj.get("drwtNo6").toString(); // 추첨번호6
				String lottery_bonus_no = obj.get("bnusNo").toString(); // 보너스번호
				
				String total_money = obj.get("totSellamnt").toString(); // 총 상금액
				String first_money = obj.get("firstWinamnt").toString(); // 1등 상금액
				String first_people = obj.get("firstPrzwnerCo").toString(); // 1등 당첨인원
				
				objMap = new HashMap<String, Object>();
				
				objMap.put("result_dt", result_dt);
				objMap.put("result_round", result_round);
				objMap.put("lottery_no1", lottery_no1);
				objMap.put("lottery_no2", lottery_no2);
				objMap.put("lottery_no3", lottery_no3);
				objMap.put("lottery_no4", lottery_no4);
				objMap.put("lottery_no5", lottery_no5);
				objMap.put("lottery_no6", lottery_no6);
				objMap.put("lottery_bonus_no", lottery_bonus_no);
				objMap.put("total_money", total_money);
				objMap.put("first_money", first_money);
				objMap.put("first_people", first_people);
				
				//방법 2
				/**
				 * 이 방법을 쓸때에는 위에 
				 * https://www.dhlottery.co.kr/common.do?method=getLottoNumber&drwNo=~~~ 로 조회한 
				 * 결과값 => ({"totSellamnt":3681782000,"returnValue":"success","drwNoDate":"2002-12-07","firstWinamnt":0,"drwtNo6":40,"drwtNo4":33,"firstPrzwnerCo":0,"drwtNo5":37,"bnusNo":16,"firstAccumamnt":863604600,"drwNo":1,"drwtNo2":23,"drwtNo3":29,"drwtNo1":10})
				 * 에 json 키값을 그대로 map에 들어가기에
				 * totSellamnt,drwNoDate 등등
				 * mainMapper.insertLottoData(objMap) 이 부분에 파라미터명들을 수정해줘야한다.
				 * 사실.. 방법 1에서 Map의 넣을 키값을 애초에 API 조회 결과값에 나온 키값으로 맞추면 다 해결된다
				  
				//요거 한줄이면 저 방법1처럼 쓴게 간편하게 맵으로 쫙 들어감.
				  
				objMap = new ObjectMapper().readValue(obj.toString(), Map.class);
				
				**/
				
				mainMapper.insertLottoData(objMap);
				
				bf.close();
				
				drwNo++; //가장 최신이 될때까지 증가
			} else {
				//최신회차가 지나 success가 뜨지않으면 while문 종료
				System.out.println("더이상 조회할 수 없습니다.");
				break;
			}
		}
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

	public List<Map<String, Object>> getLottoList() throws Exception;
	
	public int checkResultRound() throws Exception;
	
	public void insertLottoData(Map<String, Object> reqMap) throws Exception;
	
}

```

**Main_SQL.xml**

```sql

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.song.bootPjt.main.mapper.MainMapper">
	
	<select id="getLottoList" resultType="map">
		SELECT *
		FROM tb_lotto_list
	</select>
	
	<select id="checkResultRound" resultType="int">
		SELECT IFNULL(MAX(result_round),0) AS max_round
		FROM tb_lotto_list 
	</select>
	
	<insert id="insertLottoData" parameterType="map">
		INSERT INTO tb_lotto_list
					(
						result_dt, 
						result_round, 
						lottery_no1, 
						lottery_no2, 
						lottery_no3, 
						lottery_no4, 
						lottery_no5, 
						lottery_no6, 
						lottery_bonus_no, 
						total_money, 
						first_money, 
						first_people
					)
		VALUES
					(
						STR_TO_DATE(#{result_dt}, '%Y-%m-%d'),
						${result_round},
						${lottery_no1},
						${lottery_no2},
						${lottery_no3},
						${lottery_no4},
						${lottery_no5},
						${lottery_no6},
						${lottery_bonus_no},
						FORMAT(${total_money}, 0),
						FORMAT(${first_money}, 0),
						${first_people}
					)
	</insert>
	
</mapper>

```

- serviceImpl 단에 방법 1과 방법 2가 있다. 둘다 참고해보면 좋을 듯하다. 다음 글에서는 이제 main.jsp에 뿌려주는 것과 검색을 나타내보도록 하겠다.