---
layout: post
title:  "깃헙 블로그 Docker에서 작업하기"
category: Blog
tags: [docker]
---


## **계기**

깃헙 블로그를 만들기 위해서는 로컬 PC에 ruby를 설치하고 jekyll 환경구성을 해야한다. 이전에 만들었을 때는 직접 다 로컬에 설치하고 구성하였으나 PC 포맷 후 그 작업을 다시 하려니 생각보다 신경쓸 일이 많아서, 저번에 구성해 놓았던 도커로 jekyll 을 구성하기로 하였다. 5분도 안되서 모든 환경 구성이 끝났고 몇가지 명령어만 익힌다면 쉽고 빠르게 블로그를 빌드할 수 있을 것 같다.

<!-- more -->

필자는 Mac에서 Docker for mac을 설치하여 사용하였고 만약 Mac에서 설치할 방법을 찾는다면 아래에서 간단하게 볼 수 있다.

{% reference https://jgtonys.github.io/docker/2019/04/24/docker-install-mac/ %}

이제 구성한 순서에 따라서 단계별로 설명하겠다.

---

## **Jekyll-Docker 사용하기**

![7](https://user-images.githubusercontent.com/33674947/56785055-6b549380-682e-11e9-8145-a599d5f90b5b.png)


Docker가 설치되어 있고, 실행중이라면 터미널에서 다음 명령어를 쳐보자.

```bash
docker run --rm -it jekyll/jekyll /bin/bash
```

<br>

*명령어 설명*

`docker run ` : 도커를 실행한다

`--rm` : 도커 컨테이너 실행이 끝나면 컨테이너를 삭제한다

`-it` : Interactive Terminal 옵션으로 결과를 터미널에 출력한다

`jekyll/jekyll` : 도커 허브 이미지이다

`/bin/bash` : bash 로 실행한다

<br>

이 과정에서 전에 설치한 이미지가 존재하지 않다면 다음과 같이 자동으로 설치 후 컨테이너로 진입하게 된다.

<script id="asciicast-IlsHSmBLjbapntRmOzJMUwbJc" src="https://asciinema.org/a/IlsHSmBLjbapntRmOzJMUwbJc.js" async></script>

여기까지 완료되었다면 이제 도커 image 가 생성되었다. 명령어로 확인해보자.

```bash
docker images
```

![6](https://user-images.githubusercontent.com/33674947/56785054-6b549380-682e-11e9-93bf-4fd2f7305be0.jpg)

jekyll/jekyll 이미지가 생성된 것을 알 수 있다.

<br>


*만약 이미지를 잘못 설치해서 이미지를 지워야 하는 경우에는*

```bash
docker rmi -f [image-name]
```

*으로 삭제가 가능하다.*





<br>

---

## **개발 환경 설정**

필자는 로컬에 이미 지킬 블로그의 구조가 생성되어 있다. 다만 빌드할 수 있는 환경이 PC가 바뀌면서 사라졌을 뿐이다.

그러나 본 포스트에서는 처음부터 도커로 지킬 블로그를 만드는 과정을 설명하겠다.

먼저 로컬에서 지킬 블로그 파일들이 저장될 폴더로 들어가자. 그 후 터미널에 다음과 같이 입력하자. (현재 로컬에 저장될 폴더의 절대 경로가 /test 라고 가정한다)

```
docker run --rm \
--volume="/test:/srv/jekyll" \
-p 4000:4000 \
-it jekyll/jekyll /bin/bash
```

<br>

*명령어 설명*

`--volume="/test:/srv/jekyll"` : 로컬과 지킬 컨테이너 사이에 폴더를 공유한다고 생각하면 쉽다. 로컬에서는 /test 폴더, 지킬 컨테이너에서는 /srv/jekyll 의 폴더를 동기화하여 사용한다.

`-p 4000:4000` : 컨테이너의 특정 포트를 외부로 노출하는 명령어로 -p <호스트 포트>:<컨테이너 포트>의 형식으로 이루어진다. 지킬 컨테이너에서 serve 한 결과를 로컬 포트에서 확인할 수 있다.

<br>

위와 같이 명령어를 실행하면 로컬의 파일들이 지킬 컨테이너에서 보이게 된다(로컬에 아무것도 없다면 아무것도 없다). 여기에 새롭게 지킬 블로그를 만든다면 지킬 컨테이너 bash 에서 `jekyll new .` 를 입력하면 된다. 구조가 모두 만들어지면 로컬에 연결된 폴더에서도 파일들을 확인 할 수 있다.

<iframe width="853" height="480" src="https://www.youtube.com/embed/sFM0J-8OTP4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

이렇게 하면 로컬에서 모든 파일 작업들을 진행하고 jekyll 명령어를 사용해야 할 때만 터미널에서 build 하고 serve 하면 로컬에서 모든 결과를 받아 볼 수 있다! 즉 로컬에는 ruby 며 jekyll 이며 설치하지 않고 개발환경을 구성하게 되는 것이다.

<br>

## **추가 개발 환경**

필자는 로컬에서 atom 에디터로 관리하고, 도커로 지킬을 빌드한다. 변경된 파일들을 로컬에서 터미널로 푸시하거나 gitkraken을 사용해서 푸시한다. 대부분 master에 푸시하지만, 커스텀 플러그인을 만들어서 붙이거나 하게 되면 다른 브랜치에 올린 뒤 master로 올려야 한다. 이 경우에 대한 포스트는 [여기](#)에서 확인 할 수 있다.
