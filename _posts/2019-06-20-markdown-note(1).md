---
layout: post
title:  "Electron + Vuejs + Firebase 마크다운 노트앱 만들기"
category: App
tags: [electron,vuejs,firebase,markdown]
---


Electron + Vuejs + Firebase 를 사용하여 노트앱을 만드는 과정을 소개한다. 생각보다 오래걸린 솔로 프로젝트이며, 위 과정을 이해하기 위해서는 Vuejs 에 대한 문법 이해가 필수적이다. 대부분 기본적인 Vuejs 의 생태계를 알고 있다는 가정 하에 소개된다.

<!-- more -->

---





### 데모 영상

지금도 계속해서 만들고 있으나 대체적인 기능들은 모두 완성된 상태이다. 사이드 바의 경우 수정이 필요하다.


![image.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Mon%20Aug%2012%202019%2015:24:12%20GMT%2B0900%20%28KST%29.png?raw=true)

![5C418357-0607-4E62-8AE5-65C71B194EE9.png](https://github.com/jgtonys/upload_test/blob/master/screenshot-Mon%20Aug%2012%202019%2015:24:29%20GMT%2B0900%20%28KST%29.png?raw=true)




### 만들게 된 계기

필자는 공부를 하거나, 노트를 정리할 때 모든 기록 정보를 마크다운 파일에 저장한다. 이게 정리하기도 쉽고, 나중에 볼 때도 *한글파일이나 메모장* 보다 훨씬 보기가 좋고 또 코드를 많이 적어서 정리하는 과정이 편리하기 때문이다.
지난 2년간 마크다운만을 사용하다 보니, 마크다운 전용 노트 앱들을 많이 사용해 보았다. 그런데 무료 마크다운 노트앱들을 사용하다 보니 몇가지 불편한 점이 있었다. (물론 유료로 사용하면 되긴 하다)

**기존 마크다운 노트 앱들의 불편했던 점**

1. 사진파일들을 업로드 하여 노트앱에 저장하고, 그 파일을 md 파일로 export 하는 과정에서 url 이 꼬이거나, 별개의 사진파일들까지 모두 export 되어버린다.
2. 너무 무겁거나 Cross-Platform 을 지원하지 않는다.
3. 마크다운 preview의 정해진 스타일을 바꿀 수 없다 (자간, 글자 크기 등)
4. 카테고리별로 마크다운 노트들을 저장하는데에 용량 한계가 있다.


<br>

위와 같은 문제점들을 모두 해결할 수 있는 나만의 **Cross-Platfrom 마크다운 노트앱을 만들기로 했다.**

<br>
<br>

---

### Electron 으로 윈도우, MAC, Linux 어플리케이션 호환

Electorn 은 NodeJS 와 Chromium 렌더링을 사용해서 javascript 로 (거의)모든 PC 플랫폼의 어플리케이션을 개발할 수 있게 해주는 프레임워크이다. 따라서 Cross-Platform 어플리케이션을 만들기 위해서 Electron 을 선택했다.

또한 필자 같은 경우 Vuejs 를 사용해서 만드는 것이 편리해서 (여러 무료 디자인 테마들 사용이 쉬운것 같아서) Electron + Vuejs 를 사용해서 만들기로 하였다.

<br>
<br>

---

### Firebase 실시간 데이터베이스 활용으로 노트 동기화

노트앱의 필수요소는 실시간 동기화이다. 오프라인일때도 지원하면서 온라인으로 전환될 때 모든 노트의 내용이 Cross-Platform 의 다른 어플리케이션에 동기화 될 수 있어야 한다. 그렇지 않으면 그냥 로컬에 간단한 노트앱 만드는것과 다를바가 없다. **동기화는 필수이다!**

따라서 **Google Firebase 의 Firestore** 실시간 데이터베이스를 사용하여 모든 노트를 온라인에 저장하기로 하였다. 또한 오프라인 캐시를 지원하기 때문에, 따로 로컬용 데이터베이스를 구성하지 않고 (즉 firebase 말고는 db를 따로 사용하지 않고) 간단하게 만들 수 있다는 장점이 있다.

<br>
<br>

---
