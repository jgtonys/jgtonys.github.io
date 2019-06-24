---
layout: post
title:  "Linux Mint란 무엇인가"
category: OS
tags: [linux, mint]
---

**리눅스 민트** 란 초보자들에게 많이 알려져 있는 우분투 기반으로 만들어진 데스크톱 OS인데, 중요한 차이점은 우분투처럼 Unity 기반이 아니고 Gnome 기반이라는 것이다. 간단하게 말하자면 조금 더 사용자 친화적이고 **UI 및 기타 개발 환경들을 조금 더 매니아** 틱하게 바꿀 수 있다는 뜻이 된다. 다만 기존에 윈도우나 맥북에서 제공하는 것처럼 모두 셋팅된 환경이 아니라서 인프라 구성에 학을 떼지 않는 사람이 시도해야 할 것 같다.


<!-- more -->

## **알게 된 계기?**

**2017 맥프레의 구매가 좌절** 된 상황에서 집에 있는 i3 RAM 8g 512ssd 노트북을 어떻게 하면 개발 최적화 시킬 수 있을까 하다가 생각해낸 대안이다.

> 리눅스 기반의 PC로 서버 및 개발 툴들의 사용이 용이해야 함

> 리눅스 오픈소스 프로그램들의 사용이 최적화 되어야 함

> 맥북 처럼 이뻤으면 좋겠다

위 세가지의 관점에 부합하는 OS를 쇼핑하다가 리눅스 민트를 알게 되었는데, 이게 상당히 매력적인 녀석이라는 것을 글을 쓰면서 더욱 알게 되었다.

## **리눅스 민트의 종류**

*64bit 기준으로 정리된 사항입니다*

종류 | 버전 | 사이즈
:----:|:----:|:----:
Cinnamon | Linux Mint 18.3 "Sylvia" | 1.8G
Mate | Linux Mint 18.3 "Sylvia" | 1.8G
Xfce | Linux Mint 18.3 "Sylvia" | 1.7G
KDE | Linux Mint 18.3 "Sylvia" | 2G

### **공통 권장 사항**

RAM | Video | Disk
:----:|:----:|:----:
3G | 256MB | 20G


OS의 가벼움과 스피디한 작업을 기준으로 나열하였을 때 Xfce,Mate,Cinnamon,KDE 순서대로 가볍다. 현재 민트 커뮤니티에서 발표된 사항으로는 KDE판 리눅스 민트를 제거하기로 하였다고 한다. 공식적인 KDE배포판은 이제 나오지 않지만 이전의 KDE는 지원한다.(대신 보안 업데이트가 불가)


가장 인기가 좋은 Cinnamon은 나머지 배포판들에 비해 상대적으로 무겁지만 User sophisticated 하고 Gnome3 기반으로 만들어져 있다. 특히 비주얼적인 이펙트가 많아서 역동적이고 깔끔한 데스크탑 환경을 구성하는데 탁월하다. 안정성에 있어서 Mate보다는 뒤쳐지지만 모든 사소한 개발환경들을 정교하게(이쁘게) 구성할 수 있다는 점에서 많은 인기가 있다.

![Cinnamon]({{ site.baseurl }}/public/img/linux-mint-cinnamon.png)

[Linux Mint 18.3 "Sylvia" Cinnamon]


두번째로 소개할 배포판은 Cinnamon 보다 가볍고 안정성을 더 추구한 Mate 배포판이다. Cinnamon이 Gnome3 기반으로 구성된 것과 다르게 Gnome2기반으로 데스크톱 환경이 구성되어 있고, 사용자는 더욱 직관적으로 시스템 환경을 구성할 수 있다. 비록 비주얼적인 면에서 Cinnamon에 비해 구식같은 모습을 제공하지만 더 빠르고 급격한 업데이트 없이 버그 없이 안정적이라는 면에서 인기가 좋다.

![mate]({{ site.baseurl }}/public/img/linux-mint-mate.png)

[Linux Mint 18.3 "Sylvia" Mate]


세번째로 간략하게 소개할 Xfce는 현재 가장 저사양으로 환경이 구성되는 배포판이다. 소프트웨어 매니저나 필수 코덱들이 로우앤드 PC에서도 원할하게 돌아갈 수 있도록 제공하는 환경이다. 주로 특정한 목적을 위해 집에 굴러다니는 노트북을 쓸만한 개발환경으로 만들기 위해 사용하는 것 같다. 리눅스 커뮤니티에 떠도는 소문으로는 Xfce가 다음 배포판부터 삭제된다는 소문도 돌고 있다.


## **리눅스 민트의 소프트웨어**

리눅스 민트는 우분투 기반으로 만들어져 현재 독자적인 노선을 달리고 있지만, 우분투에서 돌아가는 익숙한 프로그램들은 모두 다 원할하게 지원이 된다! 여기에서는 리눅스 민트만의 소프트웨어 뿐만 아니라 우분투에서도 인기가 좋은 모든 소프트웨어를 소개한다.

### **Clementine**

*리눅스 음악전용 플레이어*

클레멘타인은 음악전용 플레이어로 스트리밍 플레이, 라디오 기능들을 지원하는 감각적인 프로그램입니다.<br>
현재 1.3.1 버전이 마지막 배포입니다.

```bash
sudo apt-get install clementine
```

![clementine]({{ site.baseurl }}/public/img/clementine.png)

### **MOC**

*터미널 음악재생 플레이어*

MOC는 매우 가볍고 실용성에 매우 충실한 음악 플레이어다. 리눅스 민트에서 개발하는 개발자들에게 무거운 음악플레이어는 부담스러운 선택일 수 있지만 이 MOC는 메모리를 1~3Mb 밖에 차지하지 않으면서도 최근의 여러가지 테마를 곁들이면 매우 감성적인 플레이어가 된다는 점에서 인기가 많다. 또한 리눅스 민트가 제공하는 코덱들과의 상성이 좋다고 알려져 있다.

```bash
sudo apt-get install moc
```

실행은 터미널에서 mocp를 입력한다.<br>
아래는 리눅스 민트와 투명터미널 Tilda를 적용한 모습

![moc]({{ site.baseurl }}/public/img/moc.png)

개발자들 눈돌아가는 소리가 들린다. 내가 이미 이 MOC에 꽃혀있는 상태다.


### **Gparted**

*리눅스 파티션 조정 프로그램*

Gnome Project 에서 관리하는 리눅스계의 파티션 제왕이다. 최근에는 버그도 많이 없고, 사용법도 간단하여 리눅스 기반 OS의 필수 소프트웨어로 자리잡았다.<br>
필요한 기능들은 모두 들어가 있고, 파일 시스템도 원할하게 지원한다.

![moc]({{ site.baseurl }}/public/img/gparted.png)


### **Atom**

*텍스트 에디터*

사실 이 프로그램은 리눅스 민트나 우분투에 한정된 것은 아니고 윈도우에서도 많이 사용한다. 다만 내가 Atom 을 좋아해서 개인적으로 추천한다.<br>
컴파일러들만 연동시켜 놓으면 감성적인 IDE가 되고 무엇보다 스스로 조작할 수 있는 패키지가 많다는 것, 또 마우스 없이 키보드로만 개발이 용이하다는 점에서 매우 추천한다.

![atom]({{ site.baseurl }}/public/img/atom.png)

### **preload**

*시스템 관리 속도 향상 프로그램*

메모리에 상주하여 프로세스 실행 속도를 효율적으로 조정해주는 프로그램이다. 최대 10프로까지의 OS 속도 향상에 기여한다는 어마어마한 녀석이므로 꼭 깔아두고 메모리를 감시하는 것이 좋다.

```bash
sudo apt-get install preload
```

### **grub-customizer**

*grub 부팅 설정 변경 프로그램*

리눅스 민트를 설치하고 나면 grub 부팅메뉴에서 멍하니 기다리는 시간이 생긴다. 이를 수정하고 추가적으로 UI를 커스터마이징 하는데 많이 쓰이는 프로그램이다.<br>
설치하기 전에 ppa install 을 선행하고 시스템 업데이트를 해주는 것이 좋다.


```bash
sudo apt-get install grub-customizer
```

![grub-customizer]({{ site.baseurl }}/public/img/grub-customizer.png)

### **compiz**

*리눅스계의 이펙트 마스터*

실용성과 화려함을 겸비한 창 관리자. 리눅스 양덕들의 말을 들어보면 이것 때문에 리눅스로 온 사람도 있다고 한다. 화려한 이펙트는 조금만 검색해도 영상으로 확인 할 수 있다.

![compiz]({{ site.baseurl }}/public/img/compiz1.JPG)



![compiz2]({{ site.baseurl }}/public/img/compiz2.png)

### **tilda**

*차별화된 CMD창*

명령창을 투명하게 만들어서 항상 디자인이 맘에 안들던 cmd를 개발하고 싶게 만들어주는 디자인 툴이다. 많은 사람들이 깔끔함에 반해 사용하고 있다.

![tila]({{ site.baseurl }}/public/img/tilda.png)

### **Synapse**

*리눅스 검색 도구*

모든 파일을 총 집합해서 빠르게 검색해주는 검색 툴이다. 이게 얼마나 편하냐 하면 음악 검색, 폴더 검색, 사진 검색, 앱 실행등의 모든 검색을 이 하나의 프로그램으로 찾고 실행할 수 있다. 즉 마우스로 바로가기나 폴더들을 뒤적뒤적 하지 않아도 된다는 뜻이다.<br>
단축키 제공으로 키보드만으로 검색 후 실행할 수 있다.

![synapse]({{ site.baseurl }}/public/img/synapse.jpg)

### **Focuswriter**

*리눅스 글쓰기 도구*

간편한 글을 쓰거나 메모가 필요할 때 이 프로그램이 제격이다. 무엇보다도 가볍고 마크다운처럼 심플하다. 여러가지 테마도 제공하고 있어서 일정이나 떠오른 아이디어를 적어 놓기에 적합하다.

![focuswriter]({{ site.baseurl }}/public/img/focuswriter.png)

### **WebStorm**

*최강 Javascript IDE*

자바스크립트 프론트 및 백앤드 개발의 최강 프로그램. 사실 Atom보다도 더 인기가 많은 녀석이다. 개발자에게 대세 IDE 프로그램은 꼭 써볼 가치가 있다고 생각한다.

![webstorm]({{ site.baseurl }}/public/img/webstorm.jpg)

### **DBeaver**

*무료 통합 SQL Editor*

무료 DB관리 프로그램 중 심플하고 강력한 툴을 제공한다. 하나의 관리 툴로 거의 모든 데이터베이스를 관리하고 CRUD를 제공한다. 물론 phpMyAdmin 이라던가 등등의 유료 DB관리 프로그램들이 훨씬 좋은 기능들과 통신을 선보이겠지만, 일단 무료로 나쁘지 않은 선택이다.

### **LibreOffice**

*리눅스 오피스 프로그램*

리눅스로 갈아타면서 나타나는 가장 큰 불편함 중 하나인 문서작업(파워포인트,엑셀,워드)의 고민을 해결해 줄 프로그램이다. 호환성이 좋아서 파일이 잘 깨지지도 않고(깨지긴 하지만) 무엇보다 무료로 사용할 수 있어서 메리트가 있다. 리눅스 민트를 사용하면 필수 프로그램이다.

![LibreOffice]({{ site.baseurl }}/public/img/LibreOffice.png)

### **Postman**

*서버 테스팅 툴*

서버를 구현할 때 http 디버깅은 항상 고달프다. 임의의 request 를 날리고 히스토리 및 해시모드를 지원하며 헤더값을 명료하게 보여줄 수 있는 툴이다. 윈도우에서 사용할 때는 크롬 확장프로그램으로 사용했는데, 리눅스에서는 우분투버전으로 다운받아서 설치 할 수 있다.

![postman]({{ site.baseurl }}/public/img/postman.jpg)

### **VNC viewer**

*원격 데스크톱 연결*

다른 PC에 원격을 접속해서 작업을 하고(예를 들어 라즈베리파이) 돌아오는 일이 매우 빈번한 나는 필수적으로 필요한 프로그램이다. 또한 AWS 나 Azure 사용에 있어서도 GUI로 확인하며 개발하기 위해서는 원격 데스크톱 연결이 필수적이다.<br>
별다를 것이 없는 일반적인 원격 연결 프로그램이다.

### **VLC player**

*리눅스 미디어 플레이어*

개발용 노트북에서 영화나 드라마를 다운로드 받아서 볼 일은 거의 없지만(스트리밍으로 봄) 그래도 미디어 플레이어가 없으면 뭔가 부족한 PC같은 느낌이 든다. 가장 무난하고 코덱문제를 빈번히 일으키지 않는 VLC플레이어를 추천한다.

![VLC]({{ site.baseurl }}/public/img/VLC.png)

---

이 밖에도 응용 소프트웨어가 아닌 개발환경 설치는 꽤나 많지만, JDK나 PIP등의 설치는 윈도우에서부터 해오던 것이기에 별다를 것이 없어서 따로 올리지 않았다.<br>
사실 그때 사용했던 명령어들이 리눅스 민트를 사용함으로 더욱 편리해지고 직관적으로 변했기 때문도 있다.

## **마치며**

사실 그냥 우분투의 Unity 환경이 너무 딱딱해보이고 정이 안가서 찾아보게 되었다가 리눅스 민트와 소프트웨어에 반해서 혹하게 된 글이다. 노트북을 리눅스 민트로 바꾸고 좀 사용해 본 다음에 꽤나 맘에들면 데스크톱도 윈10과 멀티부팅으로 바꿀 예정이다. 완벽하게 깔끔+개발 최적화 환경으로 커스텀하기에는 아직 세팅하고 배워야 할 것이 너무나도 많지만, 수많은 능력자들과 리눅스덕들의 충고를 해석하면서 도전해 보겠다.


> 환경세팅이라면 하기도 전에 피로해하는 형 왈 *"그냥 맥북을 사"*

이미 리눅스로 마음이 넘어와서 돌이킬 수가 없다.