---
layout: post
title:  "Spark Dataset Application Tutorial"
category: BigData
tags: [spark, java, database]
---


## **Spark Dataset Application**

본 포스트는 Spark 와 Java Application 을 이용하여 Dataset Application 을 구성한다. 그 전에 구성된 로컬 환경을 바탕으로 테스트 해 본다.

<!-- more -->

<br>

---

*이전 로컬환경 설정*

{% reference https://jgtonys.github.io/bigdata/2019/05/12/spark-local-setting/ %}

## **REFERENCE**

{% reference https://spark.apache.org/docs/latest/sql-getting-started.html#starting-point-sparksession %}

<br>

---

## **INITIALIZE**

Dataset application 을 만들 때 첫번째로 해야 하는 것은 `SparkSession` 클래스를 생성하는 것이다. Basic `SparkSession` 을 구성하기 위해서는 `SparkSession.builder()` 를 사용한다.

```java
import org.apache.spark.sql.SparkSession;

SparkSession spark = SparkSession
  .builder()
  .appName("Java Spark SQL basic example")
  .config("spark.some.config.option", "some-value")
  .getOrCreate();
```

<br>

---

## **Creating DataFrames**

`SparkSession` 을 사용하면, 기존에 존재하던 RDD 를 가지고 DataFrame 을 생성할 수 있다. 아래는 하나의 예시로 json 파일의 정보를 불러와서 DataFrame 을 생성하는 것을 보여준다.

```java
import org.apache.spark.sql.Dataset;
import org.apache.spark.sql.Row;

Dataset<Row> df = spark.read().json("examples/src/main/resources/people.json");

// DataFrame 의 모습을 stdout 으로 보여준다
df.show();
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+
```

위의 코드에서 `df` 는 json 으로 생성된 DataFrame 이다.

DataFrame 으로 할 수 있는 작업은 다음과 같다.

1. `printSchema()` : DataFrame 의 Schema 를 보여준다.

   ```java
    df.printSchema();
    // root
    // |-- age: long (nullable = true)
    // |-- name: string (nullable = true)
   ```

2. `Select("Attribute name").show()` : Select 한 Column 의 정보만을 보여준다.

   ```java
    df.select("name").show();
    // +-------+
    // |   name|
    // +-------+
    // |Michael|
    // |   Andy|
    // | Justin|
    // +-------+
   ```

3. `Select(col("name"), col("age").plus(1)).show();` : 임의로 정보를 수정하여
   Select 할 수 있다.

   ```java
   df.select(col("name"), col("age").plus(1)).show();
   // +-------+---------+
   // |   name|(age + 1)|
   // +-------+---------+
   // |Michael|     null|
   // |   Andy|       31|
   // | Justin|       20|
   // +-------+---------+
   ```

4. `Filter` : 연산에 의해 걸러진 정보만들 표현한다.

  ```java
   df.filter(col("age").gt(21)).show();
   // +---+----+
   // |age|name|
   // +---+----+
   // | 30|Andy|
   // +---+----+
   ```

5. `GroupBy` : 그룹화 하여 정보를 표현할 수 있다.

  ```java
   df.groupBy("age").count().show();
   // +----+-----+
   // | age|count|
   // +----+-----+
   // |  19|    1|
   // |null|    1|
   // |  30|    1|
   // +----+-----+
   ```

 <br>

 ---

## **RUNNING SQL QUERIES PROGRAMMATICALLY**

먼저 SQL Query 를 실행하기 위해서는 DataFrame 을 가상의 view 로 바꾸는 과정이 필요하다. 예를 들어서 `Select * from Table` 을 하기 위해서는 `Table` 이라는 view(table) 가 필요하기 때문이다.

다음과 같이 DataFrame 을 Temporary View 로 생성하여 사용할 수 있다.

```java
import org.apache.spark.sql.Dataset;
import org.apache.spark.sql.Row;

// DataFrame 을 SQL Temporary View 로 등록한다. df -> people
df.createOrReplaceTempView("people");

Dataset<Row> sqlDF = spark.sql("SELECT * FROM people");
sqlDF.show();
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+
```


*단 Temporary View 는 Spark Session-Scope 에서만 존재하기 때문에, Session 이 종료되면 사라지는 임시적인 View 이다.*

만약 Application 단에서 Session 이 종료되더라도 지속적으로 사용할 수 있는 View 를 생성하고 싶다면 **Global Temporary View** 를 생성해야 한다. 주의할 점은 정의한 다음 사용할 때 항상 앞에 `global_temp` 를 붙여 주어야 한다. ex) `global_temp.people`

아래는 Global Temporary View 의 예제이다.

```java
// Global Temporary View 로 등록한다.
df.createGlobalTempView("people");

// Global temporary view is tied to a system preserved database `global_temp`
spark.sql("SELECT * FROM global_temp.people").show();
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+

// Global temporary view is cross-session
spark.newSession().sql("SELECT * FROM global_temp.people").show();
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+
```

<br>

---

## **EXAMPLE RUNNING**

Spark 예제의 [JavaSparkSQLExample.java](http://javasparksqlexample.java) 를 실행해 보았다.

*Real Code*

```bash
bin/spark-submit --master spark://jungyu:7077 --class testSparkApplication.JavaSparkSQLExample testcase/testSparkApplication-0.0.2.jar
```

<br>

---

## **시연 영상**

<iframe width="853" height="480" src="https://www.youtube.com/embed/ynOzTCfrsW0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
