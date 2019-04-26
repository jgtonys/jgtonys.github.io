---
layout: post
title:  "Using Git Flow"
category: Tips
tags: [git]
---


**git flow 사용기**

음 매우 좋다.<br>
일단 알아서 develop branch 가 생성되고, 특징적인 기술을 추가로 개발할때는 feature branch가 생성된다.<br>

<!-- more -->

또 release 할때는 알아서 release branch 가 버전 태그를 달며 생성되며 끝나면 master에 병합되고 다시 develop branch 로 이동한다.<br>
<br>

**설치법**
```bash
git clone --recursive git://github.com/nvie/gitflow.git
```
그러면 이제 git flow 를 사용할 수 있다.

**주의할 점**
--recursive 옵션을 달아주지 않으면<br>
`Error:  No such file or directory @ rb_sysopen - /home/ubuntu/workspace/gitflow/gitflow-shFlags`<br>
이런 에러가 뜬다. 주의하자.

**시작하기**

git flow를 사용하려면 다음과 같이 시작한다.
```bash
git flow init
```
그러면 사용할 브랜치를 고르라고 나타나는데, 기본적으로 master 와 branch 는 주어진다.<br>
git flow를 이용하여 만들 수 있는 branch는 다음과 같다.
- master : 최종 릴리즈한 안정화 버전
- develop : 다음 릴리즈를 위해 개발중인 최신 버전
- feature : 특정 기능 개발을 위한 branch
- release : 릴리즈 점검을 위한 branch
- hotfix : 긴급 버그 픽스를 위한 branch
- support : 버전 호환성 문제 처리를 위한 branch

<br>
기본적으로 develop branch에서 개발하고, master로 realease하는 방식이다.<br>
`특정한 기능을 추가하여 개발하고 싶다면 feature를 이용한다.`
```bash
git flow feature start <branch name>
```
feature branch가 만들어지고 수정된 사항을 stage하여 commit 한다.
```bash
git stage
git commit -m
```
기능 개발이 완료 된 후에는 git flow에 개발이 끝났다고 알려주고, 다시 develop branch로 돌아가야 한다.
```bash
git flow feature finish  <branch name>
```
다음과 같은 과정이 이루어진다.
1. git flow는 develop branch로 checkout 한 후,
2. feature branch의 변경 내용을 자동으로 develop branch에 merge하고,
3. 작업이 끝난 feature branch를 삭제한다.

<br>
develop에서 개발이 완료되고 master로 병합하는 release를 하는 방법은 다음과 같다.
```bash
git flow release start <branch name>
```
release 작업을 위한 branch가 생겼다.  release branch는 보통 릴리즈 점검을 위해 간단한 버그를 수정하는 등의 작업을 목적으로 사용한다.
절대 release branch에서는 기능을 개발하거나, 기존과 다른 개발을 진행하지 않는다.(단순 확인만 하는 branch)
release를 위한 점검이 끝났으면 간단하게 realease를 finish 해주면 된다.

```bash
git flow release finish <branch name>
```
다음과 같은 작업이 이루어진다.
1. release 브랜치의 코드를 master branch에 merge
2. release의 이름으로 태그 등록
3. 릴리즈를 develop branch로 재병합(back-merge)
4. release branch를 삭제

<br>
**마치며**

git flow를 사용하면 복잡한 git의 흐름을 잘 이해할 수 있었다.
작은 프로젝트를 할 때에는 사실 이와 같은 branch 관리가 필요할까 라는 생각도 들지만, 미리미리 연습해 두는 것도 큰 프로젝트의 commit 관리에 도움이 될 것이라고 생각하여 시도해 보았다.<br>
실제로 사용해 보니 그렇게 어렵지는 않았고, 추후에 협업하여 프로젝트를 할 때에도 많은 도움이 될 것 같다.<br>
굳.









