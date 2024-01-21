---
layout: post
title: "다음메일DB저장"
subtitle: "24.01.15 ~ "
author: "Hux"
header-style: text
published: false
tags: ['python','email','decoding']
---

회사 다닐 때 갈겨쓴 코드 정리를 좀 해보려 한다. 
*~~더 이상 떠올리지 못하게 되기 전에~~*  
일하면서 써봤던 **모듈 사용법**이나 **문법 특이사항**을 기록한다.

처음은 이메일을 DB에 저장하는 프로그램이다.  
4월인가? 입사 초기에 ~~울면서~~ 만들었던 프로그램의 리메이크격이다.  

### 특징
1. 메일 서버에서 이메일 가져오기.
	- 언젠가 컴퓨터 네트워크 시간에 배웠던.. *SMTP.. 어쩌고..* 하는 것을 다시 만날 수 있었다. ~~기억의 저편에서~~
	- POP3와 IMAP 방식이 있었는데, 프로그램 요청사항이 POP3로 처리였던 것 같다. IMAP은 생각도 안하고 POP3를 사용함.
	- 패키지 함수 쓰면 문제 없이 잘 가져와진다. ~~처음 써서 헤맸을 뿐.~~
		- 사실 초기 프로그램*(4월 제작)*에는 문제가 있었는데, **전체 메시지 수를 의미하는 값** `POP3.stat()[0]`을 *메일의 고유ID*로 잘못 인지해서 프로그램이 꼬였었다. 
2. 이메일 내용 읽기.
	- 그야말로 이 프로그램의 **핵심요소**라고 할 수 있다. 
	- **너무 많은 사람들**이 **너무 많은 형식의 문자인코딩**을 사용한다...
	- 분명 이메일 인코딩을 자동으로 바꿔주는 패키지도 있지 않을까? 내가 못찾은 것이 아닐까? ~~못찾아서 결국 직접 썼다는 이야기다.~~
	- **파일 내용을 자른 다음에 인코딩을 한 녀석과.. 인코딩 한 뒤에 자른 녀석..** 그 둘의 처리에서 절망했다. 
3. DB에 저장하기.
	- DB에 저장할 때도 이러저러한 문제가 몇가지 있었는데 대부분 자잘한 문제긴 했다. 
	- 이메일 본문을 전부 저장하다보니 **`"`와 `'` 가 본문에** 있다면 당연히 **쿼리**를 작성할 때 뭔가가 고장남
	- 본문 자체 **내용이 너무 길어서 잘림**
	- **DB 테이블** 구성할 때 **유니크**를 어떻게 걸어야 할지가 고민됨
		- 뭔가 열심히 궁리해서 유니크를 잡으면 수 많은 이메일중에 꼭 뚫어내는 녀석이 나오고야 말았다.
		- 결국 엄청 주렁주렁 달아서 억지로 유니크 잡음

***

### 주요 라이브러리
- 이메일 가져오기: `email`, `poplib` 
- 인코딩/디코딩: `quopri`, `base64`
- ~~DB 전송: 파라미코로 회사 서버에 쐈는데 없으니까 생략~~

***

### 프로그램 전체 구조

~~사실 이메일도 엄청 많이 넣고 for루프 돌렸다~~

``` python
if __name__ == '__main__':
    mailClient = getMailClient(ID, PW)  # 메일서버 연결하기
    latestMailNo = mailClient.stat()[0]  # 전체 메시지 수
    ... # latestMailTime = DB에 저장된 메일 중 최신메일의 시간

    for idx in range(latestMailNo,-1,-1): #마지막 메시지부터 역순으로 조회
        # 메시지 번호로 메시지 가져오기
        msg = email.message_from_bytes(b'\n'.join(mailClient.retr(idx)[1]))
        
        # 메일정보 읽기
        (subject,mailFrom,mailTo,mailCc,mailDate), timeObj = getInfoFromMsg(msg)
        
        ... # mailDate를 latestMailTime과 비교해서 저장여부 확인
        
          # 메일본문/첨부파일 읽기
        (contentText, attachment) = getContentTextAndAttachmentFromMsg(msg)
                  
        ... # 읽은 정보를 DB에 저장하기
        
```

***

### getMailClient

메일 객체를 가져오기 위해 poplib의 POP3_SSL 함수를 사용한다. 

``` python
def getMailClient(id,pw,server:str ='pop.naver.com'):

    mailClient = poplib.POP3_SSL(server, port=995)
    mailClient.user(id)
    mailClient.pass_(pw)

    return mailClient
```

사용하는 메일 서비스에서 POP3 사용설정을 하지 않았다면 POP/SMTP settings 관련 오류가 발생할 수 있다.

### latestMailNo

많은 문제가 있었던 부분이다. 

``` python
latestMailNo = mailClient.stat()[0]
```

문서에서 stat 함수의 설명은 다음과 같다.
[POP3.stat()](https://docs.python.org/ko/3/library/poplib.html#poplib.POP3.stat)

> 우편함 상태를 가져옵니다. 결과는 2개의 정수의 튜플입니다: `(message count, mailbox size)`.


즉 위 코드에서 반환하는 값은 현재 메시지 수 이며, 이메일을 삭제했다면 변할 수 있다. 

***

### msg
``` python
msg = email.message_from_bytes(b'\n'.join(mailClient.retr(idx)[1]))
```
`mailClient.retr(idx)[1]` 를 사용하여 메일내용을 바이트스트링으로 가져온 내용을 `email.message_from_bytes()`를 통해 메시지 객체로 만들어준다. 

***

### getInforFromMsg
`msg.items()`로 이메일 정보를 가져온다. 이메일에 포함되는 여러가지 정보 중에 DB에 저장할 대상은 **제목(*subject*), 발신인(*from*), 수신인(*to*), 참조인(*cc*), 날짜(*date*)** 이다. 각각에 대한 msg.items의 요소는 다음의 예시와 같이 나타난다. 

|msg.items() 리스트 중 특정 요소|
|---|
|('Subject', '=?euc-kr?B?aVBob25lv6G8rSCzqsDHIMOjseKwoSC68ciwvLrIrbXHvvq9wLTPtNku?=')|
|('From', 'Find My <noreply@email.apple.com>')|
|('To', 'suhyeojee@gmail.com')|
|('Date', 'Sat, 20 Jan 2024 06:29:00 +0000 (GMT)')|

..! 그렇다 드디어 디코딩과 마주할 때이다. 

### 문자열 디코딩

디코딩 함수는 두 가지로 작성했다. ~~오류가 났기 때문이다~~  
누군가는 문자열을 인코딩한 뒤에 작은 단위로 잘라서 보냈고,  
누군가는 작은 단위로 자른 뒤에 각각을 인코딩해서 보냈다.  

이걸 구분할 아이디어가 없어서 그냥 함수를 2종류 쓰고 try except 잡았다.  
어차피 실제로 디코딩 하는 부분은 동일하다. 

### decodeBytes

우선 디코딩할 바이트 스트링부터 살펴보면 다음과 같다. 
```
=?euc-kr?B?aVBob25lv6G8rSCzqsDHIMOjseKwoSC68ciwvLrIrbXHvvq9wLTPtNku?=
```

1. `=?`로 시작하고 `?=`로 끝난다.  
2. 사용한 인코딩을 순서대로 `?`로 구분해서 앞쪽에 나열한다.  

따라서 바이트 스트링을 받아서 디코딩 하는 함수는 다음과 같이 작성했다. 

``` python
def decodeBytes(bytesToDecode):
    bytesToDecode = bytesToDecode[2:-2]   # =?, ?= 제거
    temp = bytesToDecode.split('?')       # 인코딩목록과 내용 분리
    bytesToDecode = temp[-1]
    encodings = temp[:-1]

    for e in encodings[::-1]:   # 인코딩목록의 뒤에서부터 디코딩
        if e.lower() == 'b':
            bytesToDecode = base64.b64decode(bytesToDecode)
        elif e.lower()  == 'q':
            bytesToDecode = quopri.decodestring(bytesToDecode,header=True)
        else:
            bytesToDecode = bytesToDecode.decode(e)
    return bytesToDecode
```

***
24.1.21 작성중