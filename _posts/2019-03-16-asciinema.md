---
layout: post
title:  "터미널 녹화하기 : Asciinema "
category: Tips
tags: [terminal]
---


## **Asciinema**

손쉽게 터미널을 녹화하여 동영상으로 만들어주는 프로그램이다. 간단한 명령어로 녹화를 시작하고 끝낼 수 있다. 녹화가 완료됨에 따라서 자동으로 웹에 업로드 되며, 로컬에 저장할 수도 있다. 또한 다른 웹에 embedded 하거나 markdown 에 넣기 편리하도록 링크도 제공한다.


<!-- more -->


<br>

---

## **설치하기**

https://asciinema.org/docs/installation

위의 링크를 보면 여러가지 설치 방법이 나와 있다.

필자는 pip 로 설치하는 방법을 택했다. 기존에 python 3.3 이상이 설치되어 있다면 문제없이 설치할 수 있다. 명령어는 다음과 같다.

```bash
sudo pip3 install install asciinema
# 또는 pip 가 python3 로 설정되어 있다면
sudo pip install asciinema
```

![complete]({{ site.baseurl }}/public/img/tips/asciinema/0.png)

<br>

---

## **사용법**

설치 후 Asciinema 를 사용하기 위해서는 인증이 필요하다.

```bash
asciinema auth
```

라고 치면 다음과 같이 링크가 나타난다.


![complete]({{ site.baseurl }}/public/img/tips/asciinema/1.png)

나타난 링크를 눌러서 들어가거나 직접 웹사이트(https://asciinema.org/)에 들어가서 회원가입을 하면 인증이 완료된다.

인증이 완료되면 녹화를 시작할 수 있다.

```bash
# 녹화 시작
asciinema rec
```

![complete]({{ site.baseurl }}/public/img/tips/asciinema/2.png)

```bash
# 녹화 종료
exit
# 또는
Ctrl-D
```

![complete]({{ site.baseurl }}/public/img/tips/asciinema/3.png)

녹화가 종료되면 `enter` 로 웹에 업로드하거나 `ctrl-c` 로 로컬에 저장할 수 있다.

매우 쉽고 빠르게 터미널의 영상을 녹화하고 공유할 수 있어서 매우 유용한 프로그램이라고 생각한다.

<br>

---

## **DEMO**

uploading..

다음은 script tag 로 embedded 된 asciinema 의 영상이다. 손쉽게 녹화하고 공유할 수 있다.

<script id="asciicast-m7hi0MfqqH2QFwKHjQ5F8GC1B" src="https://asciinema.org/a/m7hi0MfqqH2QFwKHjQ5F8GC1B.js" async></script>
