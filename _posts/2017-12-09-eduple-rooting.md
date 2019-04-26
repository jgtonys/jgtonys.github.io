---
layout: post
title:  "Eduple Military Rooting Project"
category: Tips
tags: [bash]
---

**Progress**<br>

현재 Reverse Tethering 가능<br>
Xposed installer 및 xposed framework 설치 완료(루팅필요)<br>
hack connectivity 설치 완료(루팅필요)<br>
Global rooting application 을 사용한 루팅 불가능 확인<br>
`Manual rooting` 필요

<!-- more -->


**ADB를 사용한 Eduple 제어**

* * *

- What is adb?

>Android 디버그 브리지(adb)는 에뮬레이터 인스턴스나 연결된 Android 기기와 통신할 수 있는 다목적 명령줄 도구입니다.<br>
>이 도구는 앱 설치 및 디버깅과 같은 다양한 기기 작업을 쉽게 해주고, 에뮬레이터나 연결된 기기에서 다양한 명령을 실행하는 데 사용할 수 있는 Unix 셸 액세스를 제공합니다.<br> 
>이 도구는 클라이언트, 데몬, 서버로 구성되어 있습니다.


- adb 작동방식 [here][adb-process]

- adb 실행법

기본적으로 AppData\Local\Android\sdk\platform-tool 에 있는 adb.exe로 실행한다.<br>
cmd 터미널을 열고 adb를 입력하였을 때 여러가지 옵션들이 등장한다면 성공적으로 실행한 것이다.<br>

- adb 명렁어

`adb devices` : adb가 현제 연결되어 있는 adv를 보여준다. 만약 아무것도 나타나지 않는다면 해당 장비의 개발자 옵션에서 usb debugging 을 설정하여야 한다.<br>
`adb reboot` : reboot 한다<br>
`adb shell` : shell 에 들어간다.<br>
`adb install c:\test.apk` : apk를 설치한다. -r 옵션은 재설치, -s 옵션은 메모리카드에 설치한다.<br>
`adb shell am force-stop [packagename]` : 패키지명을 이용하여 구동중인 안드로이드 어플리케이션을 강제종료한다.<br>
`adb uninstall packagename` : 패키지명을 사용하여 안드로이드 앱을 삭제한다.<br>
`adb shell pm list packages -f` : 설치된 모든 앱의 패키지명을 가져온다.<br>
`adb shell` 에서 `wm size 가로x세로` : 화면 해상도를 변경한다.<br>
`adb shell` 에서 `wm density 값` : 값으로 density를 변경한다.<br>
`adb shell` 에서 `wm size|density reset` : 해상도|density 를 리셋한다.<br>
<br>

**EdupleD Military Spec**

* * *

CPU : sun50iw1p1

CPU_brand : linux-sunxi

sunxi(arm cores) : sun50i (cortex-a53 smp)

sunxiwx (soc id) : sun50iw1 (0x1689)

soc name : A64

cores : 4xCortex-A53





[adb-process]: https://developer.android.com/studio/command-line/adb.html?hl=ko#howadbworks

