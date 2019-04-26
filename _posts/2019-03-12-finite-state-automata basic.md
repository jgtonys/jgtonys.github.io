---
layout: post
title:  "Finite State Automata Basic"
category: SCHOOL
tags: [automata]
---


## **Alphabet, Strings and Languages**

An **alphabet** is a finite, nonempty set of symbols denoted by Σ.

A **string** (or sometimes word) is a finite sequence of symbols chosen from some alphabet.

The **empty string** is the string with zero occurrences of symbols. This string, denoted by λ(in text epsilon)

<!-- more -->

The **length** of a string is the number of symbol occurrences.

The **occurrence** w σ of σ is the number of σ occurrences.

The **power** Σ^k of an alphabet is the set of strings of length k, each of whose symbols is chosen from Σ

> power 가 0 이더라도 empty string 은 존재하기 때문에 length 는 0 이 아니다.

Kleene star is all subset of the element.

<br>

---


## **Non-Empty string**

> '+' 는 1개 이상의 element 를 가지는 모든 subset 을 의미한다.

> '*' (Kleene star) 는 + 에 empty string 을 합집합한 것이다. (all possible strings)


<br>

## **Concatenation**

x와 y의 concatenation

```
xy
```

catenation operation symbol 은 생략이 가능하다.

단순히 두개의 string 을 뒤에 붙여주는 역할을 한다.

만약 lamda 를 붙이게 되면 기존의 string 과 identical 한 string 이 형성된다.

<br>


## **Language**

language 는 시그마* 로부터 선택된 set of strings 이다.

따라서 language L 은 시그마에 포함된 언어이다. 따라서 infinite 할 수도 있다.


![complete]({{ site.baseurl }}/public/img/school/automata/6.png)


<br>

### *중요*

**empty language** == **empty set**

> empty set 이면 empty language 이다. (필요충분관계)

*단 empty string(lamda) 1개를 가지고 있는 집합은 empty language 가 아니다.*

empty string(lamda) 1개를 가지고 있는 집합은 length 가 1 이다.

그러나 empty language(empty set) 은 length 가 0 이다.

*lamda 는 sigma 에 포함되지 않는다. (알파벳에 포함될 수 없다)*

<br>

## **SET**

![complete]({{ site.baseurl }}/public/img/school/automata/7.png)

*Operations*

set operation 에서 each element 는 duplicate 할 수 없다. (중복이 있으면 안된다)

![complete]({{ site.baseurl }}/public/img/school/automata/8.png)

![complete]({{ site.baseurl }}/public/img/school/automata/9.png)

*bijections*

![complete]({{ site.baseurl }}/public/img/school/automata/10.png)


<br>

---

## **Inductive Proofs**

1. Basis : 어떤 작은 문제에 대해 (직접)증명한다.

2. Inductive step : 푼 문제보다 작은 문제를 이미 푼 문제를 사용하여 가정하고 증명한다.


*Example*

n 개의 leaves 를 가지는 이진트리는 2n-1 개의 노드가 있다 를 증명하시오

1. S(1) 일 때 2*1-1 = 1 이다.

2. S(T) 일 때 더 작은 subtree U 와 V 를 두고 계산한다.


## **If-And-Only-If Proofs**

만약 X 이면 Y 이다 <=> 만약 Y 가 아니라면 X 가 아니다.

대우법(contrapositive)을 사용하는 방법이다.
