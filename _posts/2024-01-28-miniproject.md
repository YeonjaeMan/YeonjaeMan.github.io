---
title: "[미니프로젝트]멍냥이 키우기"
author: yeon
date: 2024-01-28 00:34:00 +0800
categories: [ETC, Review]
tags: [MiniProject]
---

GitHub [MiniProject](https://github.com/yeonjaeman/miniproject)

한달 동안 공부한 JAVA와 MySQL을 이용해 팀원 5명과 함께 미니프로젝트를 진행했다.   

# 프로젝트 소개

멍냥이 키우기 프로젝트는 JAVA를 사용한 시뮬레이션 게임으로, 사용자는 가상 애완동물을 선택하고 돌봐야 한다.   

텍스트 기반의 콘솔을 통해 먹이주기, 산책 등의 동작을 수행하며, 강아지와 고양이를 건강하게 키우는 것이 게임의 최종 목표이다.   

# 프로젝트 일정

- 01/23 : 프로젝트 아이디어 회의 및 산출 문서 작성   
- 01/24 : DB연동 및 기능 코드 작성
- 01/25 : 기능 코드 작성 및 오류 수정   
- 01/26 : 최종 수정 및 발표 준비

# 내가 맡은 역할

1. 유스케이스 작성
2. 로그인 및 회원가입 구현
3. JDBC를 사용해 getPetInfo() 메소드 개발
4. 게임 조건 만족 시 while문을 빠져나오도록 조건 걸기
  
## 유스케이스 작성

![유스케이스.png](/assets/img/miniproject/유스케이스.png)   

## 로그인 및 회원가입 구현

```java
// 회원가입 join() 메소드
    public int join(UserDTO dto) {
		int row = 0;

		try {
			getConn();

			String sql = "INSERT INTO cgi_23K_BIG23_p1_2.player(id, pw) VALUES (?, ?)";

			psmt = conn.prepareStatement(sql);

			psmt.setString(1, dto.getId());
			psmt.setString(2, dto.getPw());

			row = psmt.executeUpdate();

		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			getClose();
		}

		return row;
	}

// 로그인 login() 메소드
    public UserDTO login(String id, String pw) {
		UserDTO dto = null;

		try {
			getConn();

			String sql = "SELECT * FROM cgi_23K_BIG23_p1_2.player WHERE id = ? AND pw = ?";

			psmt = conn.prepareStatement(sql);

			psmt.setString(1, id);
			psmt.setString(2, pw);

			rs = psmt.executeQuery();

			if (rs.next() == true) {
				dto = new UserDTO(id, pw, 0, false);
			}

		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			getClose();
		}

		return dto;

	}
```

## JDBC를 사용해 getPetInfo() 메소드 개발

```java
// 유저의 id로 해당 유저의 펫(강아지 or 고양이)의 정보를 가져오는 getPetInfo() 메소드
    public PetDTO getPetInfo(String id) {

		PetDTO petDto = null;

		try {
			getConn();

			String sql = "select * from player inner join pet on player.id = pet.id where player.id = ?";

			psmt = conn.prepareStatement(sql);

			psmt.setString(1, id);

			rs = psmt.executeQuery();

			if (rs.next()) {
				petDto = new PetDTO(rs.getString("p_name"), rs.getString("spec"), rs.getInt("hp"),
						rs.getInt("fullness"), rs.getInt("love"), rs.getInt("money"),rs.getInt("snack"),
						rs.getInt("feed"), rs.getBoolean("supply_st"), rs.getBoolean("supply_rd"));
			}

		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			getClose();
		}

		return petDto;
	}
```

## 게임 조건 만족 시 while문을 빠져나오도록 조건 걸기

```java
while ((hp > 0 && fullness > 0 && love > 0)
		&& (hp < 75 || fullness < 75 || love < 85 || (hp + fullness + love) / 3 < 85))
```

# 아쉬운 점

처음으로 팀 프로젝트를 진행해보면서 만족감보다는 아쉬움이 더 많이 남았다.   

---

1. 프로젝트 기획 단계에서 유스케이스, 테이블명세서, 플로우 차트 등 산출 문서들을 대충 작성했던 것   

2. 설계를 제대로 안하고 넘어가서 프로젝트 방향성이 계속 바뀌어 **코드를 수정하는 일**  

3. 팀원 역할 분배에 대한 어려움

4. 코드를 덕지덕지 이어붙여 만든 프로젝트

이러한 문제를 겪지 않으려면 프로젝트 설계단계에 시간을 생각보다 많이 써서 하나하나 제대로 짚고 넘어가야 한다는 사실을 절실하게 깨달았다.   

아쉬운 점이 있다는 것이 다음 프로젝트에서 더 잘 할 수 있다는 느낌이 들고, 빨리 다음 프로젝트를 하고 싶다는 생각이 들었다.   