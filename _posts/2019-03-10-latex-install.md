---
layout: post
title:  "Latex installation [Linux Mint]"
category: SCHOOL
tags: [compiler]
---


## **Latex 란?**

문서 작성 매크로 프로그램이라고 생각하면 이해하기 쉽다. 많은 문서 프로그램들과 호환이 되며, Tex를 조금 더 쉽게 사용할 수 있도록 고안된 것이다. 수식 및 도표 삽입이 쉬워 공학 및 수학 프로그래밍 문서 작성에 매우 유용하게 사용이 되고 있다.


<!-- more -->


---


## **Installation On Linux Mint**

다음과 같은 순서로 설치가 진행된다

1. Latex 설치
2. Latex 문서 작성을 위한 에디터


## **Latex 설치**

Latex 를 설치하기 위해서는 먼저 Linux mint 에 *TeX Live* 라는 것을 설치해야 한다.

필자의 Linux mint 는 Ubuntu 16.04 버전을 기반으로 만들어진 OS 이기 때문에 다음과 같이 입력하여 TeX Live 를 설치한다.

*만약 더 최신 버전의 Ubuntu 를 기반으로 한다면, TeX Live 홈페이지에서 다운로드 받는 것을 추천한다.*


```
sudo apt-get install texlive-full
```

설치가 완료되면 다음과 같이 나타난다.

![complete]({{ site.baseurl }}/public/img/school/automata/1.png)



## **Latex 에디터 설치**

Texmaker 라는 것을 설치하여야 한다.

Texmaker 는 cross-platform open source Latex editor 로서 많은 플러그인들을 제공해주는 기본적인 Latex editor 이다.

```
sudo apt-get install texmaker
```

설치가 완료되면, Texmaker 라는 프로그램이 설치되어 있는 것을 확인 할 수 있다.

실행하면 다음과 같다.



![complete]({{ site.baseurl }}/public/img/school/automata/2.png)



## **확인 TEST**

기본적으로 PDFLatex 로 빌드(컴파일) 하게 되어있으며, 이를 실행하고 VIEW pdf 를 누르면 변환된 pdf 가 생성된다.

다음은 정상적으로 latex 를 Texmaker 가 인식하고 컴파일한 후 작성된 문서를 보여준다.

![complete]({{ site.baseurl }}/public/img/school/automata/3.png)

---

추가적으로 몇가지 이쁜 템플릿을 찾아서(보고서용으로) 둘러보다가 적용하였다.

보고서용으로 셋팅한 화면이다.

![complete]({{ site.baseurl }}/public/img/school/automata/4.png)


![complete]({{ site.baseurl }}/public/img/school/automata/5.png)


~LATEX CONFIGURATION COMPLETE~




*참고용 사이트*

> https://www.latex-project.org

> http://www.xm1math.net/texmaker

> https://www.latextemplates.com
