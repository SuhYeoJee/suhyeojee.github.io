---
layout: post
title: 파이썬 코드에 일관성을, PEP8
subtitle: "파이썬 코딩의 기술 #2"
header-style: text
published: false
tags:
  - python
  - Effective Python
---

Python Enhancement Proposal #8. 
일관성은 항상 중요한 문제다.  
앞으로 코드를 쓸 때는 신경써서 써보자..  

- 네이밍: 
	- 함수, 변수, 속성: `lowercase_underscore`
	- 보호 인스턴스 속성: `_leading_underscore`
	- 비공개 인스턴스 속성: `__double_leading_underscore`
	- 클래스, 예외: `CapitalizedWord`
	- 모듈수준 상수: `ALL_CAPS`
- 표현식과 문장:
	- `if not a is b` 보다 `if a in not b` 사용
	- `if len(somelist)==0` 보다 `if not somelist` 사용
		- somelist가 비어있으면 암시적으로 False 
		- somelist가 비어있지 않으면 암시적으로 True
	- if, for, while, except 한 줄로 쓰지 않는다.
	- import문은 최상단에
	- 모듈 가져올 때 모듈의 절대이름 사용
		- 상대적인 경우 from . import foo 사용
	- import문은 표준라이브러리 > 서드파티 > 직접정의 순서로, 알파벳 순서로
    

\+ Pylint를 사용해보자  
\+ [PEP8 - 한국어](https://zerosheepmoo.github.io/pep8-in-korean/doc/introduction.html)