---
layout: post
title:  "django 시작하기"
date:   2017-11-22 10:03:05 +0000
excerpt_separator: <!--more-->
---

**What is Django??**
<br><br>
`Django` 는 파이썬으로 만들어진 무료 오픈소스 웹 애플리케이션 프레임워크입니다. 개발자에게 회원가입, 로그인 폼, 인증 및 DB관리를 쉽게 제공하여 편리하게 웹사이트를 개발할 수 있도록 도와주는 프레임워크입니다.
<br><br>

<!--more-->

**Then What is Web Framwork??**
<br><br>
웹 프레임워크는 동적인 웹 페이지나, 웹 애플리케이션, 웹 서비스 개발 보조용으로 만들어지는 애플리케이션 프레임워크의 일종입니다. 데이터베이스 연동, 템플릿 형태의 표준, 세션 관리, 코드 재사용 등의 기능을 포함하여 개발의 편리성을 증진시키기 위하여 사용된다.
<br><br>

**test**

**python django 를 사용하여 로컬 웹서버를 띄우는 방법**
<br><br>
먼저 python django 를 설치한다.
```bash
$ pip install --upgrade pip
$ pip install django~=1.11.0
Collecting django~=1.11.0
  Downloading Django-1.11.3-py2.py3-none-any.whl (6.8MB)
Installing collected packages: django
Successfully installed django-1.11.3
```
<br>
django 가 잘 설치되었다면, python - lib - site-packages - django - bin - django-admin.py 가 존재한다.<br>
어느 경로에서나 이 django-admin.py 를 사용하기 위해 환경변수(path)에 등록한다.
<br><br>



```bash
$ django-admin startproject myproject
```
