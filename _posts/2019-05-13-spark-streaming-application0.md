---
layout: post
title:  "Spark Streaming Application Tutorial"
category: BigData
tags: [spark, java, database]
---


## **Spark Streaming Application**

본 포스트는 Spark 와 Java Application 을 이용하여 Streaming Application 을 구성한다. Streaming 을 구현하는 방법에는 Structured Streaming 과 Spark Streaming 이 있다. 먼저 Spark Streaming 을 로컬 환경에서 구성하여 본다.

<!-- more -->

<br>

---

*이전 로컬환경 설정*

{% reference https://jgtonys.github.io/bigdata/2019/05/12/spark-local-setting/ %}

## **REFERENCE**

{% reference https://spark.apache.org/docs/latest/sql-getting-started.html#starting-point-sparksession %}

<br>

---

## **LINKING**

Spark Streaming Application 의 개발을 위해서는 먼저 Maven 의 설정을 다시 바꾸어서 Streaming 을 연결해 주어야 한다.

```xml
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming_2.11</artifactId>
    <version>2.4.0</version>
</dependency>
```

만약 Kafka, Flume, and Kinesis 같은 Spark Streaming core API 에 존재하지 않는 소스로부터 데이터를 받아 온다면, artifactId 에는 다음과 같이 적어야 한다.

![E3215407-542D-4791-BA06-3E996A83865D](https://user-images.githubusercontent.com/33674947/59984216-b50cff00-9662-11e9-8d9b-1106318d56ec.jpg)

<br>

---

## **INITIALIZE**

먼저 `SparkConf` 객체 로부터 `StreamingContext` 를 생성한다.

```java
import org.apache.spark.*;
import org.apache.spark.streaming.api.java.*;

SparkConf conf = new SparkConf().setAppName(appName).setMaster(master);
JavaStreamingContext ssc = new JavaStreamingContext(conf, new Duration(1000));
```

`appName` 과 `master` 는 RDD 의 conf 에서와 같은 형식으로 넣어준다. 여기에서 Dura

만약 application 에서가 아니라 기존에 존재하는 `JavaSparkContext` 로 부터 `JavaStreamingContext` 를 생성하려 한다면 다음과 같이 할 수 있다.

```java
import org.apache.spark.streaming.api.java.*;

JavaSparkContext sc = ...   //existing JavaSparkContext
JavaStreamingContext ssc = new JavaStreamingContext(sc, Durations.seconds(1));
```

<br>

---

## **Discretized Streams (DStreams)**

DStream 이란 연속적인 데이터의 흐름이다. Source 로부터 받은 data 일 수도 있고, input stream 을 변형하면서 나타나는 처리된 data stream 일 수도 있다.

Dstream 은 일련의 연속적인 RDD 의 모습으로 나타난다.

![streaming-dstream-7dd917cd-e9a2-4a2b-a140-b1163cbde3f1](https://user-images.githubusercontent.com/33674947/59984125-c4d81380-9661-11e9-83fa-89aa9e780490.png)

DStream 에 적용된 operation 은 RDD 를 변환시킨다. Stream line 을 word 로 변환시키는 작업은 다음과 같은 변환을 나타낸다. 여기서는 `flatMap` operation 이 해당 RDD 를 변환시키게 된다.(line → word)

![streaming-dstream-ops-1273b3f9-b07d-4ecb-8582-0edea24f2183](https://user-images.githubusercontent.com/33674947/59984126-c4d81380-9661-11e9-84bf-6a80ca2f08fa.png)

이러한 변환은 Spark Engine 에 의해서 이루어진다.

정해진 hostname 과 port 에서 들어오는 TCP data 를 받는 DStream Receiver 를 생성해보면 다음과 같다.

```java
// Create a DStream that will connect to hostname:port, like localhost:9999
JavaReceiverInputDStream<String> lines = jssc.socketTextStream("localhost", 9999);
```

여기서의 lines 는 [localhost:9999](http://localhost:9999) 로부터 오는 data stream 을 받는다.

이를 word 단위로 처리하기 위해서는 다음과 같이 작성해 볼 수 있다.

```java
// Split each line into words
JavaDStream<String> words = lines.flatMap(x -> Arrays.asList(x.split(" ")).iterator());
```

여기에서 `flatMap` 은 lines Dstream 각각을 단어 단위로 쪼개어 words Dstream 으로 생성하는 역할을 한다.

또한 여기에서 각각의 word 를 count 하기 위한 코드는 다음과 같다.

```java
// Count each word in each batch
JavaPairDStream<String, Integer> pairs = words.mapToPair(s -> new Tuple2<>(s, 1));
JavaPairDStream<String, Integer> wordCounts = pairs.reduceByKey((i1, i2) -> i1 + i2);

// Print the first ten elements of each RDD generated in this DStream to the console
wordCounts.print();
```

`mapToPair` 함수는 각각의 단어를 1과 함께 묶어서 짝을 짓게 만들고 pairs 에 저장한다. 다음으로 `reduceByKey` 는 이렇게 생성된 pairs 에서 key 값에 따라 같은 값인 경우 숫자를 더해주어 wordCounts 에 저장한다.

마지막으로 `wordCounts.print` 는 매초 생성된 wordCounts 를 출력한다.

<br>

---

## **STREAMING PROCESS**

Streaming 연산을 시작하기 위해서 다음과 같이 코드를 작성한다.

```java
jssc.start();              // Start the computation
jssc.awaitTermination();   // Wait for the computation to terminate
```

`jssc.start()` 함수는 스트리밍 연산을 시작하는 것이고,

`jssc.awaitTermination()` 함수는 스트리밍 연산의 종료를 기다리는 함수이다.

연산이 종료되지 않으면 스트리밍은 멈추지 않고 계속 스트리밍을 읽기 위해 대기한다. ctrl + c 로 종료할 수 있다.

<br>

---

## **REAL CODE**

- 실제 작성된 예제 코드이다

  ```java
  package testSparkStreaming;

  import org.apache.spark.*;
  import org.apache.spark.streaming.*;
  import org.apache.spark.streaming.api.java.*;

  import scala.Tuple2;
  import java.util.Arrays;

  public final class JavaWordCount {
  public static void main(String[] args) throws Exception {
  	  SparkConf conf = new SparkConf().setAppName("testSparkStreaming").setMaster("local[2]");
  	  JavaStreamingContext jssc = new JavaStreamingContext(conf, Durations.seconds(10));
  	  JavaReceiverInputDStream<String> lines = jssc.socketTextStream("localhost", 9999);
  	  JavaDStream<String> words = lines.flatMap(x -> Arrays.asList(x.split(" ")).iterator());

  	  JavaPairDStream<String, Integer> pairs = words.mapToPair(s -> new Tuple2<>(s, 1));
  	  JavaPairDStream<String, Integer> wordCounts = pairs.reduceByKey((i1, i2) -> i1 + i2);
  	  wordCounts.print();

  	  jssc.start();              
  	  jssc.awaitTermination();   
    }
  }
  ```

이를 python 으로 랜덤 문자열을 자동으로 생성하여 [localhost:9999](http://localhost:9999) 로 전송하게 하였다. 아래는 시연 영상이다.

<iframe width="853" height="480" src="https://www.youtube.com/embed/u_S4IU7m8WI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
