---
layout: post
title:  "Real-Time Structured Streaming Privacy Preserving"
category: BigData
tags: [spark, java, database]
---


## **Real-Time Structured Streaming Privacy Preserving**

실시간으로 들어오는 빅데이터를 Spark Structured Streaming 을 사용하여 개인정보를 익명화하고 그 결과를 모니터링하는 프로젝트를 완성했다. 이전에 학습하였던 로컬에서의 application 개발을 토대로 작업하였고, privacy preserving(개인정보 익명화) 프로세스는 대그룹화, 소그룹화 익명화 알고리즘을 사용하여 구현하였다. 모니터링은 실시간으로 나오는 결과를 받아 웹으로 시각화 하였다.
본 프로젝트는 연세대 컴퓨터과학과 졸업 프로젝트로 사용되었다.

<!-- more -->

<br>

---

<iframe width="100%" height="460" src="https://www.youtube.com/embed/1aerl_Rgfzw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br>


## **1. Goal**

**Real-time privacy preserving data publishing**

"<u>privacy preserving data publishing</u> is the process of removing identifying information from data to prevent a person's identity from being connected with information."

![image (1)](https://user-images.githubusercontent.com/33674947/59985431-9c094b80-966c-11e9-8405-c7544ace5c90.png)

- Typical methods of privacy preserving data publishing are K-Anonymity and Differential privacy model based algorithms.
- We develop Apache Spark Structured Streaming-based Algorithm performing privacy preserving data publishing to Big Data.
- We research real time privacy preserving data publishing algorithms which protects against attacks resulting from the leakage of personal information during Big data circulation and analysis. At the same time, algorithms should keep the similarity of each personal information high-level.  

<br>

---

## **2. Need**

### **Why do we need real-time privacy model?**

***Importance of privacy model***

First of all, privacy model(de-identification) aims to allow data to be used by others without the possibility of individuals or organizations being identified. Personal sensitive information must be managed carefully not to be wrongly used by public especially in these big data world.

***Existing privacy model has some limitations***

There are some existing privacy models such as *k*-anonymity model or differential privacy model and these algorithm model ensures individual data not to be recognized. However, these algorithm only supports *batch processing* algorithm and this limitation relatively has some miserable results when we process lots of data stream in real-time big data input.

***Streaming process is recommended***

Nowadays, not only large amounts of data but real-time feedback of data is required to build some sensitive(reactive) system or applications.
Accordingly,  *streaming process* is extremely recommended.

***Therefore, Real-time privacy model.***

Recognizing this need which has to satisfy efficient privacy model and real-time process, we will represent privacy model supports real-time streaming process that combines de-identification algorithm and instant feedbacks using spark structured streaming.

![test](https://user-images.githubusercontent.com/33674947/59985490-f1ddf380-966c-11e9-8e63-8deca6ed94ba.png)

​	*real-time big data flooding pictures*

Streaming process is more and more efficient at processing & analyzing big data in real-time than batch processing. Below are specific advantages of streaming process.

***Low latency***

In the point of performance, the latency of batch processing will be in minutes to hours while the latency of stream processing will be in seconds or milliseconds.

***Fraud detection & Error handling***

Streaming processing is effective for coping with fraud detection and error handling. Streaming process enables real-time anomaly detecting which signals fraud sign and rolling back some fraudulent transactions before they are completed. It is important in situations when we should use error-free data processing large volumes of information or use real-time analytics which needs fast fraud-clear results.

<br>

---

## **3. Research Contents**

#### **Algorithm**

The algorithm we used to implement Real-time privacy preserving process is as follows.

1. **Transform**

![1_ (1)](https://user-images.githubusercontent.com/33674947/59985538-38cbe900-966d-11e9-88bd-8c8b8a706873.png)

Add new fields by converting the required attributes in the existing data schema.

*EX) Age -> Age Group, Full Name -> Last Name*

2. **Large Grouping**

 ![스크린샷 2019-06-19 오후 6.13.31](https://user-images.githubusercontent.com/33674947/59985541-41bcba80-966d-11e9-97ed-609386e99acd.png)

Based on the selected multiple abstraction key attributes, group of records having same attributes values are grouped together (large group) and a GID column is created.

3. **Small Grouping**

 ![스크린샷 2019-06-19 오후 6.13.38](https://user-images.githubusercontent.com/33674947/59985544-45504180-966d-11e9-86bd-69382bf77322.png)

Records having similar attribute values in one large group are grouped again into small groups.

4. **Abstraction**

![스크린샷 2019-06-19 오후 6.13.42](https://user-images.githubusercontent.com/33674947/59985547-4a14f580-966d-11e9-8b95-6253618b0a62.png)

Abstraction is performed based on the small grouping result.

<br>

**Implement**

![스크린샷 2019-06-19 오후 6.13.49](https://user-images.githubusercontent.com/33674947/59985576-76c90d00-966d-11e9-8654-5ce056d3b006.png)

There are two frameworks to implement, Spark Structured Streaming and Kafka. In the past, the batch process was done all the work in one application, but we divide it into several applications. Because Spark Structured Streaming support aggregation only once per application. So we have to find a way to send and receive data between applications. We use Kafka to connect several applications.

Spark Structured Streaming is a streaming process framework built on the Spark SQL engine. It supports Dataset/DataFreme API in Scala, Python, Java, R to express streaming aggregations, event-time windows, stream-to-batch joins. We use Java to implement each step in application. Structured Streaming provides fast, scalable, fault-tolerant, end-to-end exactly-once stream processing without the user having to reason about streaming.

Apache Kafka is a publish subscribe messaging system that is exchanging data between process, applications and servers. Kafka reduces the risk of data loss by saving message in disk not memory and Kafka is designed specifically for high-volume real-time log processing.

<br>

**Analysis**

We implement Real-time privacy preserving process successfully using these. And we also calculate various indicators between abstracted data using our process and original data.

To use processed data, it has to maintain the meaning of unprocessed data. Dissimilarity indicates how much abstracted data differ from the original data. The lower dissimilarity is, the more abstracted data maintain the meaning of original data.

If the residual rate is too low, the amount of data discarded becomes to large. Therefore, we compare and analyze the results of how to adjust the parameters to increase the residual rate.

<br>

---

## **4. Research Results**

#### **Residual rate by abstraction attributes**

![graph](https://user-images.githubusercontent.com/33674947/59985754-4a61c080-966e-11e9-8807-210e22c6f652.jpg)

It is very important to define **abstraction attributes** in the process of data abstraction. Depending on which attributes are defined as abstraction attributes, the residual rate of the data differs. In the two cases, the Abstraction results are more than twice as different. This difference comes from the abstraction
attributes. In both cases, GENDER and CITY were used as abstraction attributes commonly and one used AGE as an abstraction attributes and the other used DRINK as an abstraction attributes. Left case is poorly grouped because AGE has a wide range of values, but right case is grouped well because DRINK has only two values(0 or 1). Therefore, when using abstraction attributes with various values, the grouping will not be performed properly, so the residual rate will be low.



#### **Residual rate by the number of streaming inputs**

![image (2)](https://user-images.githubusercontent.com/33674947/59985770-649b9e80-966e-11e9-9a64-6dd477fa5792.png)

We experimented with different numbers of data(rows) per transaction and found that number of data per 1 transaction can determines amount of remnant (privacy-preserved) data. **More streaming input** per 1 transaction makes perfect “Abstraction”. When 2,000 rows were given per 1 transaction, the residual rate was 69.71%, but when 100 rows were given per 1 transaction, the residual rate is very low.



#### **How well does the result preserve the meaning of original data?**

![image (3)](https://user-images.githubusercontent.com/33674947/59985784-70876080-966e-11e9-8f71-88bd4729e325.png)

To use remnant(abstracted) data, the abstracted data should not lose the meaning of original data. The indicator of this is dissimilarity. The lower dissimilarity, the more conservative the meaning. It is calculated as follows. The results show that the dissimilarity-value is close to zero. Therefore, the abstracted data maintains the meaning of the original data well.

![image (4)](https://user-images.githubusercontent.com/33674947/59985789-79783200-966e-11e9-9933-381f6844a2a9.png)

<br>

---

## **5. Project Environment**

This is overall project environment

![Mid-Report](https://user-images.githubusercontent.com/33674947/59985878-ec81a880-966e-11e9-96e4-2efc65cb465f.jpg)

<br>

Main spark streaming application environment

![그림1](https://user-images.githubusercontent.com/33674947/59986201-a9283980-9670-11e9-96af-48ee4092d105.png)

We use Spark Structured Streaming and Kafka. In the past, the process was done all the work in one application, but we divide it into several applications. Because Spark Structured Streaming support aggregation only once per application. We use Kafka to connect several applications. (Therefore there is several kafka topics listening to previous applications)

Kafka topic listening view

<iframe width="100%" height="460" src="https://www.youtube.com/embed/_n8S1R90H4I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Spark Structured Streaming is a streaming process framework built on the Spark SQL engine. We use the Dataset/DataFreme API in Java to express streaming aggregations, event-time windows, stream-to-batch joins and implement each step in Java application. Structured Streaming provides fast, scalable, fault-tolerant, end-to-end exactly-once stream processing without the user having to reason about streaming.

Apache Kafka is a publish subscribe messaging system that is exchanging data between process, applications and servers. Kafka reduces the risk of data loss and Kafka is designed specifically for high-volume real-time log processing.


<br>

---

*Project Code*

모니터링 웹페이지 코드 공개입니다

{% reference https://github.com/jgtonys/Spark-Structured-Streaming %}


*Short Report For Project*

<iframe src="https://drive.google.com/file/d/11b_qiVLkTgvpjU3tT59YyYzQemx2VAr0/preview" width="100%" height="640"></iframe>

<br>
