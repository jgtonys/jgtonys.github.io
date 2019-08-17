---
layout: post
title:  "Jekyll 블로그에 Javascript Carousel 적용하기"
category: Blog
tags: [jekyll]
---


지킬 블로그에 Javascript 를 적용하고 메인페이지를 carousel 로 만드려고 한다. 단순하게 javascript 를 페이지에서 불러오고 css 적용시키는 법을 설명한다.

<!-- more -->


### flexslider 사용

jquery 로 동작하는 flexslider 는 pure javascript 로 carousel 을 만들어 준다. 먼저 홈페이지에서 source 파일을 다운받아 jekyll blog 폴더에 위치시킨다.

{% reference https://woocommerce.com/flexslider/ %}

소스 파일들 중 필요한 파일들은 다음과 같다(선택한것).

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Sat%20Aug%2017%202019%2016:39:07%20GMT%2B0900%20%28KST%29.png?raw=true)

이 파일들을 필자는 public 폴더에 위치시켰다.

그리고 main 화면인 index.html 에

```html
<link rel="stylesheet" href="/public/css/flexslider.css" type="text/css">
<script src="/public/js/jquery1.6.2.js"></script>
<script src="/public/js/jquery.flexslider.js"></script>
<style>
@font-face {
 font-family: 'flexslider-icon';
 src: url(/public/fonts/flexslider-icon.eot);
 src: url(/public/fonts/flexslider-icon.woff) format('woff'),
      url(/public/fonts/flexslider-icon.ttf) format('truetype');
}

</style>
<script type="text/javascript" charset="utf-8">
  $(window).load(function() {
    $('.flexslider').flexslider({
      animation: "fade",
      directionNav: true,
      slideshowSpeed: 5000,
      animationSpeed: 600,
      touch: true
    });
  });
</script>
```

다음과 같이 추가하였다. 이로서 지킬 블로그 메인 화면에서 javascript 를 불러올 수 있게 된다.

메인 화면의 carousel 적용을 위해선 flexslider 의 구조를 알아야 한다.

가장 바깥을 둘러싸는 `<div class="flexslider">` 태그가 있고, 그 안에 `<ul class="slides">` 태그가 있다. 이 `<ul>` 태그 안에 `<li>` 로 둘러쌓인 컨텐츠가 각각의 carousel 내용이 된다.

필자는 다음과 같이 메인화면을 구성하였다.

{% highlight html %}{% raw %}
<div class="flexslider">
		<ul class="slides">
      {% for post in site.posts limit:5 %}
      <li>
      <div class="post">
        <span class="post-date">{{ post.date | date_to_string }}</span>
        <h1 class="post-title">
          <a href="{{ site.baseurl }}{{ post.url }}">
            {{ post.title }}
          </a>
        </h1>
        {% if post.tags %}
        <div class="post-div">
        {% for tag in post.tags %}
        <a href="{{ site.baseurl }}{{ site.tag_page }}#{{ tag | slugify }}" class="post-tag">{{ tag }}</a>
        {% endfor %}
        </div>
        {% endif %}

        <article>
          {{ post.excerpt | markdownify }}
        <article>
        <div class="post-more">
          {% if site.disqus_short_name %}
          <a href="{{ site.baseurl }}{{ post.url }}#disqus_thread"> <i class="fa fa-comments" aria-hidden="true"></i>Comment</a>&nbsp;
          {% endif %}
          <a href="{{ site.baseurl }}{{ post.url }}"><i class="fa fa-plus-circle" aria-hidden="true"></i>Read more</a>
        </div>
      </div>
      </li>
      {% endfor %}
		</ul>
	</div>
{% endraw %}{% endhighlight %}
