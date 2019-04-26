---
layout: post
title:  "Mac에 Docker 설치"
category: Docker
tags: [docker]
---


## **Docker 란?**

Docker란 컨테이너 기반의 오픈소스 가상화 플랫폼이다. 거창하게 말하면 그렇지만 간단하게 말해서 많은 서버와 프로그램의 환경을 컨테이너로 추상화하여 사용자의 PC에 직접 설치하는 일 없이 가상환경으로 돌려주는 플랫폼을 의미한다. 최근의 많은 개발 작업들은 의존성 패키지들이 버전도 다양하여 환경 셋팅만 하루종일 걸리고 정작 실제 개발은 시작도 하지 못하고 지쳐버리는 현상이 나타나는데, 이 환경 셋팅을 아주 간편하게 가상화하여 사용할 수 있는 녀석이다.


<!-- more -->

설치 방법은 굉장히 간단하므로 쉽게 따라할 수 있다.

## **Docker 설치**

*영상은 공용 와이파이에서 다운로드해서 설치가 느리다..*

<script id="asciicast-2UQJqnVpnmsm2GSCQiAAIM1Hf" src="https://asciinema.org/a/2UQJqnVpnmsm2GSCQiAAIM1Hf.js" async></script>

Mac에서 Docker를 설치하려면 먼저 서버와 클라이언트를 설치해야 한다.

<br>

**클라이언트 설치**

클라이언트 설치는 터미널에서 다음과 같이 입력한다.

```
brew install Docker
```

설치를 모두 완료한 후 정상적으로 클라이언트가 설치되었는지 확인하기 위해 다음과 같이 입력한다.

```
docker version
```

그러면 클라이언트가 설치되었다는 것을 확인할 수 있고, 서버는 아직 설치되지 않았음을 확인 할 수 있다.

![2](https://user-images.githubusercontent.com/33674947/56633253-52b27500-6698-11e9-8ba3-bbead7e8b52b.jpg)

<br>

**서버 설치**

서버를 설치하기 위해 [여기](https://docs.docker.com/docker-for-mac/install/) 에서 설치 파일을 다운로드 한다.

간단한 회원가입 후 다음과 같이 다운로드 한다.

![1](https://user-images.githubusercontent.com/33674947/56633121-e0da2b80-6697-11e9-998c-ae71286cf5fa.jpg)

dmg 파일이 정상적으로 다운로드 되면, 실행시킨 후 application 폴더로 옮겨서 바로가기를 만든다.

![3](https://user-images.githubusercontent.com/33674947/56782596-c4b6c580-6822-11e9-8fe0-cc5f6e8f3bcc.jpg)

그 후 실행시키면 서버가 켜진다.

![4](https://user-images.githubusercontent.com/33674947/56782597-c4b6c580-6822-11e9-86e0-407336ca3be2.jpg)

서버가 켜졌는지 확인하기 위해 다시 `docker version` 을 확인해 보면 다음과 같이 나타난다.

![5](https://user-images.githubusercontent.com/33674947/56782598-c4b6c580-6822-11e9-9c02-b8db73c7474c.jpg)


<br>

---

이렇게 맥에서 Docker의 설치가 끝났다.
