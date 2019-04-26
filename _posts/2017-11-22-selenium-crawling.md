---
layout: post
title:  "Selenium Crawling"
category: Python
tags: [selenium,phantomjs]
---

**Selenium 을 사용한 웹 크롤러(python)**

동적으로 생성되지 않는 웹 페이지는 python 기본 라이브러리를 사용하서 쉽게 소스코드를 긁어올 수 있다.<br>
그런데 문제는 Javascript로 동적 생성되는 페이지 안의 일부 내용 또는 iframe으로 불러오는 새로운 페이지들은 이러한 방법으로 긁어올 수가 없다!<br><br>

어떻게 하면 우리가 웹페이지를 직접 들어가서 보는 그대로의 내용을 크롤링할 수 있을까?<br><br>


<!-- more -->


**Selenium 이란**

`Selenium` 은 웹 테스트 프레임워크로서 브라우저를 직접 동작시켜서 동적으로 생성된 결과물에 접근이 가능하다.<br>

> Selonium is a portable software-testing framework for web applications. Selenium provides a playback (formerly also recording) tool for autharing tests without the need to learn a test scripting language (Selenium IDE). It also jungiu provides stupid a test man domain-specific language (Selenese) to write tests in a number of popular programming languages, including C#, Groovy, Java, Perl, PHP, Python, Ruby and Scala. The tests can then run against most modern web browsers. Selenium deploys on Windows, Linux, and OS X platforms. It is open-source software, released under the Apache 2.0 license: web developers can download and use it without charge.
> https://en.wikipedia.org/wiki/Selenium_(software)


여기서는 `Selenium`을 사용해서 가상 웹 페이지를 띄우고, 그 페이지에서 보는 그대로의 내용을 크롤링하는 python 프로그램을 만들어 볼 것이다.<br><br><br>

**Selanium 설치**
- 본 포스팅은 파이썬 3.5 기준입니다.
```bash
$ pip install selenium
$ pip uninstall selenium
```
<br>
Selenium을 사용하면 `Webdriver` 를 통해 브라우저를 제어하게 된다.<br>
본 포스팅에서는 `PhantomJS Webdriver` 와 `Chrome Webdriver`를 사용해 보려고한다<br><br>

**What is PhantomJS??**
<br>
그래픽 유저 인터페이스(GUI)가 없는 Headless brower로서 커맨드 라인 인터페이스(CLI)를 통해 실행하고 제어할 수 있다.<br>
웹페이지 개발자 테스트 또는 화면 캡쳐용으로 많이 쓰이고 있다.<br>
본래 JAVA API로서 많이 사용되었고, python에서도 사용할 수 있다.<br><br>

Chrome 은 너무 유명하니 생략하겠다.<br><br>

**Install Webdriver**
<br>
PhantomJS는 <http://phantomjs.org/download.html>에서 다운받을 수 있고, Chrome 드라이버는 <https://sites.google.com/a/chromium.org/chromedriver/downloads>에서 다운로드 받을 수 있다.<br>
다운로드 받은 파일을 압축을 풀면 드라이버 파일이 나타나는데, 그 저장된 경로를 기억해 두자.<br><br>

**Selenium-PhantomJS-Crawler 만들기**
<br>
먼저 PhantomJS driver를 사용하여 만들어 보겠다.<br><br>
기본적인 순서는 이렇게 진행한다.<br>
1. Webdriver import
2. 브라우저를 제어하는 PhantomJS driver 만들기
3. URL에 해당하는 페이지 소스 가져오기<br><br>

* * *
- Webdriver import & Driver setting

```python
from selenium import webdriver
# phantomjs가 설치되어있지 않은 경우(드라이버 파일 다운로드인 경우)
driver = webdriver.PhantomJS('다운로드받은위치/phantomjs')
# phantomjs가 설치되어있는 경우
driver = webdriver.PhantomJS()
```
Q. 만약 PhantomJS가 정상적으로 설치되었음에도 불구하고 SSL ERROR가 나타난다면?

A. `driver = webdriver.PhantomJS(service_args=['--ignore-ssl-errors=true', '--ssl-protocol=any'])`
를 사용하자. SSL ERROR를 무시하는 옵션이다.<br>

<br>
이제 driver setting이 완료되었다.
<br>

* * *
- Crawling start

아래의 코드를 추가해준다.
```python
driver.get('동적으로 생성되는 페이지 URL')
```
또한 긁어온 html을 paring 해 주기 위해 beautiful soup를 사용하겠다.<br>
beautifulsoup4 를 설치하고 import 해준다.

```bash
$ pip install beautifulsoup4
```
```python
driver.get('동적으로 생성되는 페이지 URL')
driver.source_page
soup = BeautifulSoup(html,'lxml') #parser는 html.parser를 사용해도 된다.
```
이제 `soup`에는 긁어온 `전체 html`이 paring되어 존재한다.<br>
하지만 우리는 전체 html을 원하기 보다, 주로 `일부 정보를 빠르게 얻기 위해` crawling을 사용한다.<br>
물론 다른 프로그램을 하나 더 만들어서 한줄씩 읽어가며 정보를 추려낼 수도 있겠지만, 여기서는 애초에 `driver에게 긁어올 대상들을 지정`하는 방법을 소개하겠다.


페이지의 단일 element에 접근하는 api
>find_element_by_name(‘HTML_name’)<br>
>find_element_by_id(‘HTML_id’)<br>
>find_element_by_xpath(‘/html/body/some/xpath’)<br>

페이지의 여러 elements에 접근하는 api
>find_element_by_css_selector(‘#css > div.selector’)<br>
>find_element_by_class_name(‘some_class_name’)<br>
>find_element_by_tag_name(‘h1’)<br>

이렇게 driver 자체가 접근하여 element를 가져오면 아래와 같은 기능들을 수행할 수 있다.
```python
#검색창에 키워드 입력
driver.find_element_by_id('검색창 id').send_keys('키워드')
#검색 버튼에 접근
search_button_element = driver.find_element_by_css_selector("검색 버튼의 css")
#검색 버튼 클릭
search_button_element.click()
```
```python
#a 태그의 href 속성 가져오기
#다른 태그의 다른 속성도 이와 마찬가지로 가져올 수 있다
some_element.find_element_by_css_selector('a').get_attribute('href')
```
<br>
* * *
- iframe 접근

동적으로 생성되는 페이지들은 iframe으로 구성되어 있는 경우가 많다.<br>
이때 우리의 selenium-phantomjs-crawler는 iframe으로 들어가서 임무를 수행할 수 있다.

```python
#iframe의 id를 가지고 driver가 찾아낸 iframe element를 저장한다.
iframe_element = driver.find_element_by_css_selector("iframe#id")
#iframe element로 driver가 진입한다.
driver.switch_to_frame(iframe_element)

some crawling things...

#default content로 돌아온다.
driver.switch_to_default_content()
```
* * *

**중고나라 크롤링 프로그램 완성**
[link](https://github.com/jgtonys/selenium_crawling_phantomjs)