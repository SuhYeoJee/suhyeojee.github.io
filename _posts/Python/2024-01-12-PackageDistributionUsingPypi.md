---
layout: post
title: "Pypi 패키지 배포 - [API token 사용]"
subtitle: "『 Username/Password authentication is no longer supported. 』"
header-img: "img/post-bg-css.jpg"
header-img-credit: "@WebdesignerDepot"
header-img-credit-href: "medium.com/@WebdesignerDepot/poll-should-css-become-more-like-a-programming-language-c74eb26a4270"
published: false
header-mask: 0.3
tags: [Pypi, Guideline]
---


> 회사 다닐때 만든 싸제 모듈이 있었다.

... Pypi에 등록하는 과정이 번거로울 것 같아서 *매번 ~module.py 파일을 복사*해서 썼다. 
버전관리.. 같은건 하지 않았다. 그때그때 수정해서 쓴 부분도 있어서..
~~슬슬 그 녀석이 재앙이 될 때가 된 것 같다. 미안하다.~~
아무튼 이번에 그 녀석을 정리할 겸 **Pypi 등록 과정**을 진행했다.


## 패키지 구성

> 폴더생성

일단 프로젝트 명으로 새 폴더를 만든다. 
그리고 하위에 패키지명으로 새 폴더를 만든다. 
두 폴더 이름이 같아도 상관 없으므로 둘 다 패키지명인 igzg으로 정했다. 

|`igzg(프로젝트 폴더)`|
|:---|
|ㄴ`igzg(패키지명 폴더)`|

 <br>

> 주요 함수를 `.py` 파일로 작성 

실제 모듈에는 여러 파일에 여러 함수와 클래스가 필요하겠지만, 일단 테스트 함수 하나만 ~~대강~~ 작성해서 패키지를 구성했다. 
사용한 파일 명은 `mySQL.py`이다.
```py
def getInsertQuery(...):
    ...
```

<br>

>`__init__.py` 파일 생성

내용은 없어도 된다. 아주아주 기본적인 정보를 작성해두었다. 
```py
__version__ = '0.0.0'
__all__ = ['mySQL']
```

지금까지 진행도는 다음과 같다. 

|`igzg(프로젝트 폴더)`|
|:---|
|ㄴ`igzg(패키지명 폴더)`|
|　ㄴ`__init__.py`|
|　ㄴ`mySQL.py`|

<br>

> 패키지 설명을 `setup.py`에 작성


이번에도 내용은 별거 없다.  
setuptools 모듈의 setup과 find_packages를 사용한다.
```py
from setuptools import setup, find_packages
from igzg import __version__ as igzgVersion

setup(
    name = 'igzg',
    version = igzgVersion,
    description = 'PYPI tutorial package creation written by suhyeojee',
    author = 'suhyeojee',
    author_email = 'suhyeojee@gmail.com',
    url = 'https://github.com/suhyeojee/igzg',
    packages = find_packages(exclude=[]),
    package_data = {},
    include_package_data = False,
    zip_safe = False,
    classifiers = [
        'Programming Language :: Python :: 3',
        "License :: OSI Approved :: MIT License",
        "Operating System :: Microsoft :: Windows",
    ],
    python_requires = '>=3.6',
    install_requires = [],
)
```

작성한 `setup.py` 파일은 프로젝트 폴더 하위에 둔다.

|`igzg(프로젝트 폴더)`|
|:---|
|ㄴ`igzg(패키지명 폴더)`|
|　ㄴ`__init__.py`|
|　ㄴ`mySQL.py`|
|ㄴ`setup.py`|
 
<br>

이것으로 패키지 구성은 완료이다.


## Pypi 인증토큰 발급

> [pypi 계정 관리](https://pypi.org/manage/account/) 에서 API token 발급

API tokens 메뉴의 ADD API token 버튼을 클릭해서 토큰 생성페이지로 이동한다.  

토큰 이름을 입력하고, 토큰의 적용범위를 선택한다.
이름은 본인이 구분할 수 있는 것으로 자유롭게 작성하고, 범위는 등록되어있는 프로젝트를 선택하거나 전체 프로젝트를 선택할 수 있다. 

![](/img/in-post/Python/240112_Pypi/pypiToken.png)



## .pypirc 파일 생성

> 컴퓨터의 홈 디렉토리에 .pypirc 파일 생성
> 기본 경로:  `C:\Users\Windows10`

pypi에 패키지 배포시 .pypirc 파일의 정보로 사용자 인증을 진행한다.
`.pypirc` 파일의 내용은 다음과 같이 작성한다. 

```
[pypi]
  repository = https://upload.pypi.org/legacy/
  username = __token__
  password = < pypi-로 시작하는 API token >
```



## 패키지 빌드 & 업로드

> 작성한 .py 파일로 배포용 빌드 생성

빌드 생성을 위한 패키지가 설치되어있지 않다면 다음을 실행한다.
```
pip install setuptools wheel
```

`setup.py`가 위치한 프로젝트 폴더에서 다음을 실행한다.
```
python setup.py sdist bdist_wheel
```

실행 결과 build, dist, 등 파일이 생성된다.

|`igzg(프로젝트 폴더)`|
|:---|
|ㄴ`build`|
|ㄴ`dist`|
|ㄴ`igzg.egg-info`|
|ㄴ`igzg(패키지명 폴더)`|
|　ㄴ`__init__.py`|
|　ㄴ`mySQL.py`|
|ㄴ`setup.py`|
 
<br>

> 패키지 업로드

업로드를 위한 패키지가 설치되어있지 않다면 다음을 실행한다.
```
pip install twine
```

프로젝트 폴더에서 다음을 실행한다.
```
twine upload -r pypi dist/*
```

실행결과  

![](/img/in-post/Python/240112_Pypi/uploaded.png)


## 등록한 패키지 확인

> 패키지 다운로드

pip을 사용해서 등록한 패키지를 다운받는다.
```
pip install --upgrade igzg
```

![](/img/in-post/Python/240112_Pypi/downloaded.png)

> 동작 확인

테스트 코드를 작성해서 모듈 동작을 확인한다.
```py
from igzg.mySQL import getInsertQuery

q = getInsertQuery('tableName',{'data1':'value1','data2':'value2'},True,['data1'])

print (q)
```
결과
```
INSERT INTO `tableName` (`data1`,`data2`) VALUES ('value1','value2') ON DUPLICATE KEY UPDATE `data1`='value1';
```

---

## 완료! 

이걸로 어디에서나 이것저것 모듈을 사용할 수 있게 되었다.

![](/img/in-post/Python/240112_Pypi/pypiPage.png)