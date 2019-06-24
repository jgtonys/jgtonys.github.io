---
layout: post
title:  "Spark RDD Application Tutorial"
category: BigData
tags: [spark, java, database]
---


## **Spark RDD Application**

본 포스트는 Spark 와 Java Application 을 이용하여 RDD Application 을 구성한다. 그 전에 구성된 로컬 환경을 바탕으로 테스트 해 본다.

<!-- more -->

<br>

---

*이전 로컬환경 설정*

{% reference https://jgtonys.github.io/bigdata/2019/05/12/spark-local-setting/ %}


## **REFERENCE**

{% reference https://spark.apache.org/docs/latest/rdd-programming-guide.html %}

<br>

---


## **ENVIRONMENT**

*Spark 2.4.0* supports `lambda expressions` for concisely writing functions, otherwise you can use the classes in the `org.apache.spark.api.java.function` package.

Java 를 Spark application 에 연결하기 위해서는 Spark application 의 maven 에 dependency 를 추가해 주어야만 한다. (이미 되어있는 경우 생략)

```
groupId = org.apache.spark
artifactId = spark-core_2.11
version = 2.4.0
```

만약 HDFS 를 연결한다면 마찬가지로 maven 에 dependency 를 추가해 주어야 한다.

```xml
groupId = org.apache.hadoop
artifactId = hadoop-client
version = <your-hdfs-version>
```

**Java 를 Link 하기 위해서는 다음과 같이 class 들을 import 한다**

```java
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.SparkConf;
```

<br>

---

## **INITIALIZE**

Spark application 을 만들 때 첫번째로 해야 하는 것은 `JavaSparkContext` 객체를 생성하는 것이다. 이 객체는 어떻게 cluster 에 연결할 수 있는지를 명세한다. `SparkContext` 를 생성하기 위해서는 먼저 `SparkConf` 객체를 생성하여 application 의 정보를 가져온다.

1. SparkConf 객체로 정보 획득

2. SparkContext 객체 생성

   SparkConf conf = new SparkConf().setAppName(appName).setMaster(master);
   JavaSparkContext sc = new JavaSparkContext(conf);

`appName` : Name for your application to show on the cluster UI

`master`  : Spark, Mesos or YARN cluster URL, or local (또는 spark-submit 을 통해 나타난 master 의 이름)

*Java Spark Application 은 shell 을 통해 initialize 할 수 없다. 따라서 코드에서만 가능하다.*

## **Resilient Distributed Datasets (RDDs)**

병렬로 처리가 가능한 장애 감내(결함 감수)  속성들의 집합이다. RDD 를 만드는 방법에는 크게 두가지가 존재하는데, 하나는 존재하는 데이터를 병렬로 처리하는 것이고, 다른 하나는 외부 Storage, input 으로 부터 dataset 을 참조하는 것이다.

1. Internal Collection → RDD

`JavaSparkContext` 의 기능 중 하나인 *pararellize* 를 사용하면 기존의 집합을 병렬적으로 처리하여 RDD 생성이 가능하다.

*List data 를 RDD 로 만드는 과정(parallelize)*

```java
List<Integer> data = Arrays.asList(1, 2, 3, 4, 5);
JavaRDD<Integer> distData = sc.parallelize(data);
```

- PARTITION : Spark application 이 task 를 수행하는 단위로서 cluster 를 나누어 사용하는 단위이다. 보통 하나의 CPU 당 2~4 개의 partition 이 존재한다. 만약 partition 이 지정되지 않았다면 Spark 가 자동으로 할당하지만 다음과 같이 임의로 partition 을 할당할 수도 있다. `sc.parallelize(data, 10)`← 10개의 partition.

2. External Dataset → RDD

`JavaSparkContext` 의 *textfile* 을 사용하면 외부 dataset 을 RDD 로 생성이 가능하다.

*Text file 을 RDD 로 만드는 과정*

```java
JavaRDD<String> distFile = sc.textFile("data.txt");
```

*Local 에서 실행할 경우 파일은 local filesystem 내에서 접근이 가능해야 한다*

Partition 의 경우 textfile method 를 사용하면 input file 의 한 block 마다 partition 이 생성된다.(default)

여러가지 다른 Format 의 파일

- wholeTextFiles : 디렉토리 단위로 읽어서 내부 파일들까지 모두 읽는다
- sequenceFile : Key 와 Value 를 가지고 읽는다.
- hadoopRDD : Hadoop 의 inputFormat 을 읽는다.
- JavaRDD.saveAsObjectFile : RDD 를 java Object 로 저장한다.

<br>

---

## **RDD OPERATION**

Transformation : lazy 하게 동작하여 실제로는 값을 변경하지 않는 예약작업

Action : Task 를 실제로 실행하게 되는 작업

```java
JavaRDD<String> lines = sc.textFile("data.txt");
JavaRDD<Integer> lineLengths = lines.map(s -> s.length());
int totalLength = lineLengths.reduce((a, b) -> a + b);
```

`map` 은 transformation 이고, `reduce` 는 action 이다.

`reduce` 가 실행되는 시점에서 spark 는 partition 을 실행하고 `map` 을 실행하게 한다.

만약 위의 코드에서 `lineLengths` 를 메모리에 저장하여 한번 실행 후 다시 재사용 하고 싶다면 `lineLengths.persist(StorageLevel.MEMORY_ONLY());` 코드를 추가한다.

*Lambda function usage*

```java
class GetLength implements Function<String, Integer> {
  public Integer call(String s) { return s.length(); }
}
class Sum implements Function2<Integer, Integer, Integer> {
  public Integer call(Integer a, Integer b) { return a + b; }
}

JavaRDD<String> lines = sc.textFile("data.txt");
JavaRDD<Integer> lineLengths = lines.map(new GetLength());
int totalLength = lineLengths.reduce(new Sum());
```
