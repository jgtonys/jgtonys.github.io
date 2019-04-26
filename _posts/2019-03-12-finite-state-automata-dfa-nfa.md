---
layout: post
title:  "Finite State Automata FAs"
category: SCHOOL
tags: [automata]
---


## **FAs**

Finite-State Automata 를 의미하며, 많은 application 이 FA로 만들어져 있다.

여기에선 FA의 특성을 알아보기 위해 application 을 간략하게 다루어 볼 것이다.

<!-- more -->


<br>

---

## **Switch**

![complete]({{ site.baseurl }}/public/img/school/automata/11.png)

automata 는 주로 자신이 어디에 있고, 무엇을 하는지 보여준다.

*Example(vending machine)*

![complete]({{ site.baseurl }}/public/img/school/automata/12.png)

위와 같이 state 를 change 하며 결과값을 선택해 나갈 수 있다.

위의 그림에서는 많은 오류가 있다. (환불 불가, 가격에 따른 종류 변경 불가)

<br>

## **Automata**

오토마타란?

프로그램 가이드라인을 따라서 만들어지는데, 오토마타는 language recognizer 라고 불리운다.

![complete]({{ site.baseurl }}/public/img/school/automata/13.png)

Input 이 주어졌을 때 유효한지 알아보고(accept) 그렇지 않으면 거부한다(reject).

Double circle 로 나타난 state 는 final state 라고 불리운다.

자기 자신을 가리키는 transition 은 self transition 이라고 불리운다.

> 위의 그림에서 가능한 input 은 inifinite 하다.

automata 는 따라서 language 를 represent 한다. (set of string 을 받아들이기 때문이다. set of string == language)

automata 가 인식하는 language 는 regular language 이다.

외부의 input 으로부터 움직이는 것을 제어하는 역할을 한다.

<br>

## **Deterministic Finite-State Automata (DFAs)**

각 state 마다 하나의 움직임만이 가능하다.(unique 하게)

유한한 state 의 개수가 있다.

![complete]({{ site.baseurl }}/public/img/school/automata/14.png)

<br>

*Example*

![complete]({{ site.baseurl }}/public/img/school/automata/15.png)

위의 그림에서 3 은 sink state 이다. 왜냐하면, 이 state 로 간 경우 다시 빠져나올 수 없기 때문이다.

final state 는 sink state 가 될 수 없다.(accpet 를 해야 하기 때문이다)

> QUIZ : lanugage 를 주고 DFA 를 디자인하라고 할 것이다.

<br>

*DFA Configuration*

만약 crash 가 일어난 경우 현재 state 와 남은 input string 을 가지고 unfinished computation 을 resume 할 수 있도록 한다.

![complete]({{ site.baseurl }}/public/img/school/automata/16.png)

위의 그림에서는 state 3 와 7 에서 언제든지 다시 시작할 수 있다.

<br>

*Single-Step computation*

앞의 example 에서 input 이 `aaa` 라면 다음과 같이 표현이 가능하다

```
(0,aaa)
(1,aa)
(2,a)
(2,lamda)
```

이 변환의 과정은 다음과 같은 automaton 으로 표현한다.

![complete]({{ site.baseurl }}/public/img/school/automata/17.png)

<br>

*Multiple-Step Computation*

![complete]({{ site.baseurl }}/public/img/school/automata/18.png)

<br>

*Acceptance*

만약 얼마간의 automation 이후 lamda 가 나타났다면, 그 state 는 final state 이다.

![complete]({{ site.baseurl }}/public/img/school/automata/19.png)

Language L(A) 라는 것은 A가 모든 accepted string 의 set 라는 것을 의미한다.

## **Determinism vs Nondeterminism**

![complete]({{ site.baseurl }}/public/img/school/automata/20.png)

NFA 란 경로가 하나가 아닌 것을 의미한다. 반대로 DFA 는 단하나의 경로가 존재하는 것이다.

<br>

## **NFA Specification**

기본적으로 DFA와 같지만 transition 의 결과가 여러개로 나타난다.

![complete]({{ site.baseurl }}/public/img/school/automata/21.png)

*Example*

![complete]({{ site.baseurl }}/public/img/school/automata/22.png)

<br>

## **DFAs and NFAs**

DFA 의 language 와 NFA 의 language 는 같다. 비록 모양이 다르고 방식이 다르더라도 language 는 같다.

## **Subset Construction**

![complete]({{ site.baseurl }}/public/img/school/automata/23.png)

*Correctness*

![complete]({{ site.baseurl }}/public/img/school/automata/24.png)
