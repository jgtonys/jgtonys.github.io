---
layout: post
title:  "Docker 이미지 만들기 & 배포하기"
category: Blog
tags: [docker]
---


필자는 깃헙 블로그 jekyll 환경을 docker 로 작업하고 있다. 한가지 번거러운 점이 있다면, 매번 이 jekyll 컨테이너를 열고 특정 플러그인을 위해 `gem install metainspector` 를 해 주어야 하는 것이다. 만약 이 패키지까지 설치되어 있는 이미지를 만들어서 배포하면, 어디서든 나만을 위한 docker image 를 불러올 수 있을 것이다.

<!-- more -->

---

[지난번 포스트](https://jgtonys.github.io/blog/2019/04/25/docker-jekyll/) 에서는 docker 를 실행하고 작업하는 일련의 과정을 나타내었다. 이번에도 환경은 정확하게 동일하나, image 를 만드는 과정을 소개한다.

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Tue%20Aug%2013%202019%2018:43:56%20GMT%2B0900%20%28%EB%8C%80%ED%95%9C%EB%AF%BC%EA%B5%AD%20%ED%91%9C%EC%A4%80%EC%8B%9C%29.png?raw=true)


## Dockerfile 만들기

Dokcerfile 은 기존의 docker 이미지로부터 새로운 이미지를 만들 때, 설명서의 역할을 해주는 파일이다. 나만의 이미지를 만들고 싶다면 반드시 만들어야 하는 파일이다. *Dockerfile 은 따로 확장자가 존재하지 않는다*

간단한 문법은 다음과 같다.

- `FROM` : 어떤 base 컨테이너로부터 만들어 지는 이미지인지 기술한다
-  `RUN` : bash 쉘에서 입력하는것과 동일한 명령어를 기술한다
-  `ADD` : 호스트의 파일 시스템으로부터 파일을 가져와서 이미지에 더한다

필자는 이 명령어들을 가지고 다음과 같은 `Dockerfile` 을 생성했다.

```
FROM jekyll/jekyll
RUN gem install metainspector
RUN echo "Welcome to jgtony's blog compiler"
```

## Docker 이미지 빌드

이제 앞서 만들어진 Dockerfile 을 가지고 나만의 이미지를 만들 시간이다. Dockerfile 이 있는 경로로 이동해서 다음과 같이 명령어를 치자.

````
docker build -t githubjekyll:0.0 .
````

여기서 githubjekyll 는 이미지의 이름이며 0.0 은 버전 태그가 된다. 또한 맨 마지막의 `.` 은 현재 있는 폴더의 Dockerfile 을 사용하겠다는 뜻이다.

이렇게 빌드가 끝나게 되면, `docker images` 를 통해 확인해보자.

새롭게 githubjekyll 이미지가 생성된 것을 확인할 수 있다.


## Docker 이미지 배포

이렇게 만들어진 githubjekyll 은 로컬에만 존재해 있다. 다른 PC로 이동해서 docker 이미지를 검색해도 나오지 않는 이미지이다. 따라서 이 이미지를 **배포** 하려 한다.

먼저 https://hub.docker.com/ 사이트로 이동한다

{% reference https://hub.docker.com %}

로그인을 하고 Repositories 로 이동하면 나의 REPOSITORY 를 확인할 수 있다.

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Tue%20Aug%2013%202019%2018:44:48%20GMT%2B0900%20%28%EB%8C%80%ED%95%9C%EB%AF%BC%EA%B5%AD%20%ED%91%9C%EC%A4%80%EC%8B%9C%29.png?raw=true)

**Create Repository 를 클릭한다**

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Tue%20Aug%2013%202019%2018:48:02%20GMT%2B0900%20%28%EB%8C%80%ED%95%9C%EB%AF%BC%EA%B5%AD%20%ED%91%9C%EC%A4%80%EC%8B%9C%29.png?raw=true)

**이름과 설명을 작성하고 Create 를 누른다**

이제 다시 커맨드 창으로 돌아와서 기존에 만든 githubjekyll 이미지를 github_jekyll repository 에 배포해보자.

먼저 `docker login` 을 통해 docker hub 에 로그인해주고 `docker tag githubjekyll:0.0 jgtony/github_jekyll:0.0` 으로 태그를 달고 이미지를 생성해주자!

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Tue%20Aug%2013%202019%2018:51:21%20GMT%2B0900%20%28%EB%8C%80%ED%95%9C%EB%AF%BC%EA%B5%AD%20%ED%91%9C%EC%A4%80%EC%8B%9C%29.png?raw=true)

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Tue%20Aug%2013%202019%2018:51:38%20GMT%2B0900%20%28%EB%8C%80%ED%95%9C%EB%AF%BC%EA%B5%AD%20%ED%91%9C%EC%A4%80%EC%8B%9C%29.png?raw=true)

위와 같이 완료가 되었다면 이제 docker hub 에 배포해보자! 명령어는 `docker push jgtony/github_jekyll:0.0` 이다. 완료 후 페이지에 가서 확인하면 다음과 같이 올라와 있다.

![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Tue%20Aug%2013%202019%2018:53:45%20GMT%2B0900%20%28%EB%8C%80%ED%95%9C%EB%AF%BC%EA%B5%AD%20%ED%91%9C%EC%A4%80%EC%8B%9C%29.png?raw=true)

**배포가 완료되었다. **

## Reference

{% reference https://jungwoon.github.io/docker/2019/01/13/Docker-7/ %}
{% reference https://blog.naver.com/PostView.nhn?blogId=alice_k106&logNo=220646382977&parentCategoryNo=7&categoryNo=&viewDate=&isShowPopularPosts=true&from=search %}
