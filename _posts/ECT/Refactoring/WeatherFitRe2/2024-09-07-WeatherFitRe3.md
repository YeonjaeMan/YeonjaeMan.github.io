---
title: "WeatherFit 프로젝트 이미지 데이터 CRUD 리팩토링"
author: yeon
date: 2024-09-08 11:10:00 +0900
categories: [ECT, Refactoring]
tags: [Project, Refactoring, JSP, Servlet, Base64]
---

얼마 전 WeatherFit 프로젝트에서 이미지 데이터를 로컬 환경에 저장하던 방식을 DB에 저장하고, 꺼내오는 방식으로 바꿨다.

## 이유
이러한 방식으로 바꾼 이유는 프로젝트 당시 이미지 데이터 처리에 대해 고민했었는데, 보통 기업에서는 이미지나 동영상 데이터를 DB에 저장하기에는 효율적이지 못하고, 파일만 따로 저장하는 파일 시스템을 이용한다는 것을 알게되었으나 당시 배포된 DB 하나와 짧은 프로젝트 기간.. 덕분에 파일 시스템보다는 DB에 이미지를 저장하고자 했었다.

하지만, blob 타입이라고 하는 byte 배열 형태로 이미지를 저장하는 것까지는 성공하였으나 Ajax를 통해 이미지를 화면에 렌더링하는 것에 실패했었다.

결국, 프로젝트 완수를 위해 로컬 환경에 이미지를 저장하고 해당 이미지 경로를 불러오는 방식으로 처리했다.

나중에 프로젝트 끝나고 혼자서 해보자는 다짐을 했었고, 취업을 준비하는 이번 기회에 해결할 수 있었다.

## DB에 이미지 저장 핵심 코드
[https://github.com/2024-SMHRD-KDT-BigData-23/WeatherFit/commit/431ce802adb4e46381b7062e08e5572dd23bfee2]

```java
File file = new File(realPath + "/" + multipartRequest.getFilesystemName("postImg"));
byte[] fileImg = new byte[(int) file.length()];
try(FileInputStream fis = new FileInputStream(file)) {
    fis.read(fileImg);
}
fvo.setFileImg(fileImg);
```

1. MultipartRequest로 받아온 postImg를 File 객체로 불러온다.
2. byte[] 형태로 fileImg를 File 객체의 크기만큼 생성한다.
3. FileInputStream을 통해서 file을 읽어온다.
4. DB에 insert문을 보내기 전 FileVO의 setter를 사용해 setting한다.

이러한 과정을 통해서 사용자가 게시글을 작성하면 이미지가 DB에 저장될 수 있게 하였고 이에 맞게 Update, Delete 관련 로직과 Mapper의 Query를 손쉽게 처리할 수 있었다.

## 이미지 Read 작업
```java
// Blob 데이터를 Base64로 인코딩
if(resultFileVO != null && resultFileVO.getFileImg() != null) {
	byte[] fileImg = resultFileVO.getFileImg(); // Blob 데이터를 byte 배열로 가져오기
	String base64Image = Base64.getEncoder().encodeToString(fileImg); // Base64 인코딩
	result.put("file", base64Image); // Base64 문자열을 result에 추가
} else {
	result.put("file", null); // 파일이 없는 경우 null 추가
}
```

Read 작업에서 생각해야 될 것은 JSP에서 사용하는 HttpServletRequest, HttpServletResponse 객체가 아닌 Ajax를 사용해서 `JSON` 형식으로 데이터를 보내주는 것이다.

이미지를 저장할 때 MultipartRequest에서 받아온 이미지를 byte[] 타입으로 바꾸어주었다면 JSON 형태로 이미지를 응답하기 위해선 byte[] 타입을 `Base64`라고 하는 객체로 `인코딩`해주어야 한다.

```html
<img id="post-img" src="data:image/jpeg;base64, ` + map.file + `" class="img-fluid mx-auto d-block">
```

더불어 html img의 src속성에 base64 형태라는 명시를 통해 화면에 이미지가 띄워진다.

> Base64는 64진법의 의미를 가지고 있는데 2의 6제곱 (2^6=64)으로 ASKII 문자들로 표시할 수 있는 가장 큰 진법이다. 덕분에 데이터 교환에 많이 쓴다.

이러한 과정을 통해 게시글의 이미지와 프로필 이미지 처리를 완료했다.

![complete_img](/assets/img/WeatherFitRe3/complete_img.png)

![complete_img2](/assets/img/WeatherFitRe3/complete_img2.png)