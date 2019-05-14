---
layout: post
title:  "텍스트마이닝: 전반적인 지식"
category: BigData
tags: [textmining]
---


## **텍스트마이닝이란?**

텍스트 정보처리라고도 불리우며 구조화되지 않은(unstructured) 대규모 텍스트 집합에서 새로운 지식 또는 의미를 발견하는 과정이다.

<!-- more -->

![textmining_review](https://user-images.githubusercontent.com/33674947/57694133-e7036c80-7685-11e9-9f53-42270604bb66.jpg)

<br>

---

## **구조화되지 않은 데이터**

1. Unstructured Data 는 내부 구조는 있으나, 미리 정의된 데이터 모델이나 스키마에 의해 구조화 되지 못한 데이터이다.

2. 그 데이터들은 텍스트일수도 있고 아닐수도 있으며 인간에 의해 만들어졌을 수도 있고, 기계가 자동으로 만들었을 수도 있다.

3. 또한 이 데이터들은 NoSQL 같은 non-relational db 에 저장될 수도 있다.

> Unstructured data has internal structure but is not structured via pre-defined data models or schema. It may be textual or non-textual, and human- or machine-generated. It may also be stored within a non-relational database like NoSQL.

<br>

**인간이 만든 unstructured data 의 예시**

- 텍스트 파일
- 이메일
- Social Media
- 웹사이트
- Mobile data
- Communications
- Media
- Business applications

**기계가 만든 unstructured data 의 예시**

- Satellite imagery: Weather data, land forms, military movements.
- Scientific data: Oil and gas exploration, space exploration, seismic imagery, atmospheric data.
- Digital surveillance: Surveillance photos and video.
- Sensor data: Traffic, weather, oceanographic sensors.

<br>

---

## **Semi-Structured data**

내부 태그들과 마킹들이 별개의 데이터 요소를 식별할 수 있도록 하는 데이터이다. 주로 정보를 그룹화하고 계층화한다.

Ex) XML, JSON

<br>

**XML**

인간과 기계가 읽을 수 있는 형식으로 인코딩 규칙을 적용한 문서이다. 태그에 의해 구조가 정의되며 사용이 매우 유연하다.

> Set of document encoding rules that defines a human- and machine-readable format. Its tag-driven structure is highly flexible

<br>

**JSON**

자바스크립트 객체를 나타낼 때 사용한다. name/value 로 이루어지거나 배열의 형태를 띌 수도 있으며 언어간에 구조 변환이 가능하다. 웹과 서버 사이에서 데이터를 주고받는데 사용된다.

> JavaScript Object Notation. Name/value pairs (or object, hash table, etc.) and an
ordered value list (or array, sequence, list). Interchangeable structure among languages. Transmitting data between web applications and servers

<br>

---

## **텍스트 정보처리과정**

1. 수행하고자 하는 목표 설정
2. (텍스트)데이터 수집
3. (텍스트)데이터 전처리: 데이터클리닝, 자연어처리
4. (텍스트)데이터 탐색
5. (텍스트)데이터 분석: 통계분석(회귀분석), 오차(기계학습)

<br>

---

## **텍스트 마이닝의 과정**

![2](https://user-images.githubusercontent.com/33674947/57694129-e66ad600-7685-11e9-8681-47db038c177e.jpg)


<br>

---

## **TM vs DM**

![3](https://user-images.githubusercontent.com/33674947/57694130-e66ad600-7685-11e9-905c-8212b2dd0ec6.jpg)

<br>

Machine Learning 의 관점에서는 다음과 같이 나타난다.

**Data Mining**

- 데이터셋 식별 (Identify data sets)
- 특징 선택 (Select features)
- 데이터 준비 (Prepare data)
- 분석 (Analyze distribution)

**Text Mining**

- 문서들을 식별 (Identify documents)
- 특징 추출 (Extract features)
- 알고리즘으로 특징 선택 (Select features by algorithm)
- 데이터 준비 (Prepare data)
- 분석 (Analyze distribution)

<br>

---

## **텍스트의 구조**

텍스트는 먼저 문헌집합(Collection or Corpus)으로 이루어져 있다. 그리고 각각의 문헌 또는 문서(Document)는 문단(Paragraph)과 문장(Sentence)으로 이루어져 있다.

문장의 구조는 다음과 같이 어절(Word phrase) -> 형태소(Morpheme) -> 음절(Syllable) -> 음소(Phoneme)로 쪼갤 수 있다.

![4](https://user-images.githubusercontent.com/33674947/57694132-e7036c80-7685-11e9-8e58-a169e597dfb0.jpg)

<br>

**의존 구문 분석(Dependency Parse)이란?**

각 단어들이 다른 단어의 요소에 의해 의존적으로 영향 받는 상태를 분석하는 것을 의미한다.

<br>

---

## **텍스트 분석**

**Natural Language Processing(NLP)**

자연어 처리 NLP 는 `Corpus > Document > Paragraph > Sentence > Token` 순서로 처리한다.

토큰 레벨(Token Level)에서의 분석법으로는 Ontologies, Inexact Match, Regular Expression 을 사용하고 문서 레벨(Document Level)에서는 Classification, Clustering 을 사용한다.

<br>

**분석의 문제점(또는 어려운 점)**

1. Access : 모든 소스가 통합되어 있지 않다. 각기 다른 저장소를 가지고 다른 어플리케이션에 존재한다.

2. Management : unstructured data 의 관리는 매우 부실하고, 처리가 필요하다. 또한 불필요 더미 데이터가 너무 많고 보안에 취약하다. 또 다양한 언어로 구성되어 있다.
