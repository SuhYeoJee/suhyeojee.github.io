---
layout:     post
title:      "깃블로그 포스트 헤더 작성"
navcolor: invert
catalog: true
published: false
hidden: true
header-mask: 0.3
tags:
    - HOWTO
    - BLOG
---

# 포스트에 제목 작성하는 방법

일반적으로 사용하는 헤더는 이미지/ 텍스트 두 가지  
iframe을 넣은 경우도 있었는데 쓸 일이 있을지는 모르겠다.   

```
---
layout: post
title:      "TEXT TITLE"
subtitle:   "SUBTITLE"
header-style: text
---
```
![](/img/in-post/240119_PostHeaderSetting/textTitleExample.png)


```
---
layout: post
title:      "IMG TITLE"
subtitle:   "SUBTITLE"
header-style: img
---
```
![](/img/in-post/240119_PostHeaderSetting/imgTitleExample.png)

***

# 옵션 리스트

|옵션명|내용|기본값(생략하는경우)|
|---|---|---|
|date|날짜|파일명 날짜를 따름|
|author|작성자|블로그 이름 (bayaheuro)|
|hidden|메인에 표시 여부|true|
|catalog|태그 검색 포함 여부|true|
|published|발행 여부|true|
|header-img|이미지 지정|블로그 메인테마|
|header-mask|배경 불투명도|0|
|tags|리스트로 태그 나열|-|
|iframe|타이틀에 아이프레임 넣기|-|
|navcolor|네비게이션 컬러|-|
|header-bg-css|헤더에 css 지정|-|
