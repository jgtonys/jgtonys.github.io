---
layout: post
title:  "깃헙 블로그에 Latex 적용하기"
category: BlOG
tags: [latex]
---


## **Latex 적용법**

깃헙의 마크다운은 네이트브로 수식 입력을 지원하지 않으므로, MathJax 를 연결하여 사용할 수 있도록 하자. *Jekyll 로 만든 Github Blog* 의 경우 따로 커스터마이징 하지 않았다면 대부분 `_layouts` 라는 폴더가 존재할 것이다. `_layouts/post.html` 이라는 파일은 blog post 를 작성할 때 이 구조를 따르게 되는데, `<article>` 태그 안에는 우리가 Markdown 으로 적은 내용(contents)이 들어가게 된다. 여기에 다음과 같은 script 코드를 넣어주자.

<!-- more -->

```html
<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
```

이렇게 넣으면 이제 모든 post 의 contents 에서 mathjax 을 인식하고 latex 문법을 사용할 수 있게 된다.

블로그에 수식을 쉽게 넣을 수 있도록 하자!


**예시**

Given a DFA~$A=(Q, \Sigma, \delta, s, F)$, where
$$Q = \{1,2,3,4,5,6\}, \Sigma = \{a,b\}, s = 1, F = \{2,5,6\}$$ and
$\delta$
is defined as follows:

---

## **Latex 문법 정리**

https://ko.wikipedia.org/wiki/위키백과:TeX_문법
