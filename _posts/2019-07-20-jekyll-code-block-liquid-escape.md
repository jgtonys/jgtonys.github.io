---
layout: post
title:  "Jekyll Code Block 에서 liquid tag Escape"
category: Blog
tags: [jekyll]
---


깃헙 블로그로 지킬을 사용하고 있는 필자는, 블로그 포스팅에 `code block` 을 많이 사용한다. 근데 코드를 작성할 때 이중괄호를 사용하거나 이중 배열을 사용하게 되면, jekyll 이 compile 을 하면서 `liquid tag` 로 인식해 버린다. 어떻게 `code block` 의 `highlighting` 을 살리면서 `liquid tag` 를 escape 할 수 있는지 알아보자.

<!-- more -->




먼저 {% raw %}{{0,0}}{% endraw %} 이런 배열을 선언했다고 가정하자. 그럼 jekyll 에서는

![A69D192A-BAF5-4AFA-A557-717865DDEE46.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Sat%20Aug%2017%202019%2012:42:33%20GMT%2B0900%20%28KST%29.png?raw=true)

syntax error 를 내뱉어 버린다.

### 해결방법

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Sat%20Aug%2017%202019%2013:06:25%20GMT%2B0900%20%28KST%29.png?raw=true)

태그를 사용한다. 이 태그는 그냥 raw 하게 text 를 출력해주는 역할을 한다. 그래서 안에 들어간 내용물을 compile 해야하는 태그로 생각하지 않고 text로 여겨 raw 한 내용물을 출력하게 된다.

다음과 같이 나타난다.

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Sat%20Aug%2017%202019%2012:44:40%20GMT%2B0900%20%28KST%29.png?raw=true)

아 근데 위와 같이 코드가 그냥 텍스트로 나타나게 되면, 굉장히 가독성이 떨어진다.

**original text 를 보여주면서 highlighting 이 가능하려면 다음과 같이 태그를 작성한다.**

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Sat%20Aug%2017%202019%2013:07:13%20GMT%2B0900%20%28KST%29.png?raw=true)

이렇게 태그를 작성하게 되면, 안의 내용물은 raw 하게 출력하면서 이 code text 를 가지고 highlight 를 진행한다.

*올바르게 코드가 나타나는 것을 확인할 수 있다*

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Sat%20Aug%2017%202019%2012:58:03%20GMT%2B0900%20%28KST%29.png?raw=true)
