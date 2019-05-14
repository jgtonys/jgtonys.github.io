---
layout: post
title:  "텍스트마이닝: Scraping 기초 지식"
category: BigData
tags: [textmining, python, html]
---


## **텍스트(데이터) 수집 방법**

python을 사용하여 데이터를 크롤링하는 방법을 배운다. 본격적으로 코드에 대해 언급하기 전에 알아야 할 인코딩이나 환경에 대하여 먼저 서술한다.

<!-- more -->

(사실 시험대비용)

<br>

---

## **인코딩 방식**

1. ASCII : 최초의 문자열 인코딩 방식으로 7bit으로 구성되어 128개 문자를 표현한다. 일본어나 중국어 등 다른 언어의 표현이 불가능하여 후에 다른 언어 지원을 위해 만들어진 인코딩이 *ANSI* 이다.

2. ANSI : ASCII의 확장판으로 8bit로 구성되어 ASCII에서 1bit 를 추가해 여기에 다른 언어 문자를 표현했다. 그러나 새로 추가된 128개 문자로는 모든 언어의 표현이 불가능하여 CodePage 라는 개념이 탄생했다. 영어가 아닌 다른 언어를 사용할 경우에는 Code Page 를 동일하게 맞춰 주어야 한다.

> CodePage : 각 언어별로 Code 를 주고 Code 마다 다른 문자열 표를 의미하도록 약속하였다.

3. EUC-KR : 한글 지원을 위해 유닉스 계열에서 나온 완성형 코드 조합이다. 한국에서 ANSI를 확장한 것이다.

> 완성형 코드란 완성된 문자 하나하나 마다 코드 번호를 부여하는 것이고 반대 개념으로 조합형 코드가 있다. 조합형 코드는 자음과 모음으로 나누어 초성 중성 종성을 조합하는 것이다.

4. CP949 (Code Page 949) : 한글 지원을 위해 윈도우즈 계열에서 나온 확장 완성형 코드 조합이다. EUC-KR 이 2byte로 표현할 수 있는 문자의 수에 한계가 있어 확장하여 만든 것이다. 949는 페이지 번호를 의미하고 한국을 의미한다. 기본적으로 EUC-KR과 호환되며 마이크로소프트가 만들었다고 MS949라고도 한다.

5. UTF-8 : 유니코드를 위한 가변 길이 문자 인코딩(멀티 바이트) 방식이다. ANSI의 Code Page 를 이용하는 방식(단점)을 보완하기 위해 만들어졌다. 하나의 character set 에 거의 모든 문자를 넣을 수 있다. 첫 128자는 ASCII 코드 값으로 ASNI와 동일하다. 또한 영어를 사용할 경우에는 1byte 만 사용한다. 한국,중국,일본 등 동아시아권 언어는 3byte 이상을 사용하고 이때 UTF-8 은 비효율적일 수 있다.

> 멀티바이트란 표현해야 하는 문자에 따라 글자 크기를 가변으로 변경해서 사용하는 것이다. ANSI는 고정바이트로 최대 256자까지만 표현이 가능하지만, UTF-8은 최대 1112064자까지 표현이 가능하다. (더 많은 문자 표현 가능)

6. UTF-16 : 16bit 기반으로 저장하는 UTF-8의 변형으로 한글을 UTF-16으로 저장하면 2byte가 된다. (UTF-8에서는 3byte) 그러나 때때로 2byte 이상을 사용하기 때문에 용량에 이점이 있다고 볼수는 없고, ANSI와 호환이 안되거나 복잡하여 문제가 있다. 옛날 한자나 한글 고어를 표현할 수 있다. 참고로 UTF-32는 모든 문자를 4byte로 인코딩한다(매우 비효율적).

7. UNICODE : 전 세계의 문자를 일관되게 표현할 수 있도록 고안된 코드 조합으로 전 세계 모든 문자를 2byte 숫자로 1:1 매핑시켰다.

> UTF-8과 유니코드는 엄연히 다르다.


<br>

**Python 문자열 다루기**

string 을 다루는 것은 나중에 stemming 이나 lemmitization 에 활용된다.

- 문자열 결합(concatenation) : '+'
- 문자열 반복 : '\*'
- 타입 변환 : str(),int()
- 인코딩 : name(), lookup()

```python
>>> import unicodedata
>>> unicodedata.name('a')
'LATIN SMALL LETTER A’
>>> snowman = '\u2603'
>>> unicodedata.lookup(unicodedata.name(snowman)) '☃’
>>> snowman.encode(‘utf-8’)
```

- 문자열 변형 : split(), replace()
- 정규표현식 : 복잡한 문자열을 처리할 때 사용. 메타문자 사용

**의존 구문 분석(Dependency Parse)이란?**

각 단어들이 다른 단어의 요소에 의해 의존적으로 영향 받는 상태를 분석하는 것을 의미한다.

메타문자[] 란 문자 클래스를 의미한다. 예를 들어, [abc]라면 이 표현식의 의미는 "a,b,c중 한 개의 문자 와 매치”이다. [a-c] 는 문자 사이의 범위를 이용한다. [^0-9] 는 숫자가 아닌 문자를 의미한다(반대).

자주 사용하는 문자 클래스는 다음과 같다.

- \d - 숫자와 매치, [0-9]와 동일한 표현식
- \D - 숫자가 아닌 것과 매치, [^0-9]와 동일한 표현식
- \s - whitespace 문자와 매치, [ \t\n\r\f\v]와 동일 – 맨앞의 빈칸은 공백문자(space)를 의미
- \S - whitespace 문자가 아닌 것과 매치 – [^\t\n\r\f\v]와 동일
- \w - 문자+숫자(alphanumeric)와 매치 – [a-zA-Z0-9_]와 동일
- \W - 문자+숫자(alphanumeric)가 아닌 문자와 매치 – [^a-zA-Z0-9_]와 동일

Dot(.)은 줄바꿈 문자 \n 을 제외한 모든 문자를 의미한다. 예를 들어 a.b는 a+모든문자+b 이다. 만약 a[.]b 라면 문자 그대로 a.b 만을 일치시킨다.

반복 ({m,n}, ?)은 m부터 n까지 반복하는 것을 의미한다.

```text
– {3,}처럼 사용하면 반복 횟수가 3이상인 경우
– {,3}처럼 사용하면 반복횟수가 3이하인 것 – 생략된 m은 0과 동일
– 생략된 n은 무한대(2억개미만)의 의미
– {1,}은 +와 동일하며 {0,}은 *와 동일
```

<br>

python re 모듈 : 정규표현식을 python 에서 사용할 수 있도록 해준다.

```python
>>> import re
>>> p = re.compile('[a-z]+')

# match() : 문자열의 처음부터 정규식과 매치되는지 조사
>>> m = p.match("python")
>>> print(m)
>>> m = p.match("3 python")
>>> print(m) #조건에 부합하지 않으므로 리턴값 NONE
```

- match() : 문자열의 처음부터 정규식과 매치되는지 조사
- search() : 문자열 전체를 검색하여 정규식과 매치되는지 조사
- findall() : 정규식과 매치되는 모든 문자열(substring)을 리스트로 리턴
- finditer() : 정규식과 매치되는 모든 문자열(substring)을 iterator 객체로 리턴

<br>

참고로 python 의 자료형 중 list 는 변경이 가능하고, tuple은 불변이다. dict 는 key와 value 로 구성되어 있다.

tuple.jpg

<br>

---

## **웹 데이터의 종류**

바이너리 데이터 : 2진수로 표현된 데이터

텍스트 데이터 : 문장이나 글자로 구성된 데이터. 텍스트 데이터도 바이너리 데이터이다.

<br>

**수집의 3단계**

1. 대상 선정 : 목적 데이터의 위치 파악
2. 데이터 수집 : 대상 데이터 수집(데이터 수집 자동화)
3. 데이터 정리 : 수집된 데이터 정리

수집할 때 유의사항으로는 저작권, 리소스의 과다, 업무방해, API 규칙 준수 가 있다.

<br>

---

## **스크래핑/크롤링**

**스크래핑이란?**

웹 사이트에 있는 특정 정보를 추출하는 기술이다. 데이터베이스에 저장하기 위해 데이터 가공이 필요하고, 사이트의 구조를 분석해야 한다. 따라서 스크래핑은 데이터 추출 + 데이터 구조 분석 으로 정의된다.

**크롤링이란?**

웹 사이트의 링크를 타고 돌며 데이터를 긁어 저장하는 기술이다. 크롤러 또는 스파이더라고 한다.

<br>

스크래핑 및 크롤링을 하기 위해서는 웹의 구조를 분석해야 한다.

<br>

---

## **HTTP**

Hyper Text Transfer Protocol로 하이퍼텍스트 뿐만 아니라 이미지나 동영상, 음성 등을 전송하는데 사용된다. 웹 브라우저는 HTTP 사용자 에이전트 중 하나이다.

http.jpg

**HTTP request의 구조**

request.jpg

request2.jpg

<br>

Request Header 는 부가적인 정보를 서버에 제공할 때 name 과 data 쌍으로 표현한다. 부가정보의 성격을 가지며 HTTP 1.1버전에서는 host에 대한 정보가 필수적이다.

<br>

**HTTP response의 구조**

response.jpg

response2.jpg

<br>

Response Header 도 마찬가지로 name 과 data의 쌍으로 표현되며 주요 header name으로는 다음과 같은 것들이 있다.

- P3P (Platform for Privacy Preference)
- Content-Type: - MIME type of content in response body
- Content-Length: - Length of content in response body

<br>

**Python 웹 클라이언트 라이브러리**

urllib 은 python 에서 url 을 통해 데이터를 가져오는 기능을 제공한다. httplib 은 get, post 이외의 방식으로 요청을 보내거나 시간을 지연시키는 기능이 필요할때, http 프로토콜 요청의 저수준 세밀 기능이 필요할 때 사용한다.

```python
from urllib.request import urlopen
f = urlopen(“http://www.example.com”)
print(f.read())
````

<br>

urlparse 는 url을 분해하고 조립하고 변경처리를 하게 해준다. 말 그대로 url parsing

```python
from urllib.parse import urlparse
result = urlparse(“http://www.example.com”)
print(result)
```

<br>

---

## **HTML Parsing**
