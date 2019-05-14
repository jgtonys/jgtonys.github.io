---
layout: post
title:  "Spark & Java Application Local 설정"
category: BigData
tags: [spark, java, database]
---


## **Spark 로컬 환경 구성**

본 포스트는 Spark 와 Java Application 을 이용한 Structured Streaming 구현을 목표로 한다. 방대한 양의 빅데이터가 실시간으로 들어오는 상황에서 database의 관리를 batch processing 이 아닌, streaming 으로 처리하기 위함이다. 그 첫번째 과정으로 로컬 환경을 구성하여 테스트 해 본다.

<!-- more -->

<br>

---

# SPARK LOCAL SETTING

local spark settings, java application settings

<br>

---

### REQUIREMENT

Spark, Eclipse(Maven)

<br>

---

### WHAT IS MAVEN

*개발 프로젝트 시 나타나는 많은 의존성 라이브러리들을 관리해 주는 툴*

또한 MAVEN 은 사용할 라이브러리에 필요한 다른 의존성 라이브러리들을 자동으로 다운로드하여 개발 환경에 셋팅할 수 있도록 해준다.

<br>

---

### MAVEN INSTALLATION

Eclipse 를 설치하게 되면 자동으로 설치된다. (따로 설치할 필요 없음)

<br>

---

### MAVEN LIFECYCLE

메이븐은 프로젝트 생성에 필요한 단계(phases)들을 Build Lifecycle이라 정의하고 `default`, `clean,` `site` 세가지로 표준 정의한다. Lifecycle은 Build Phase 들로 구성되며 일련의 순서를 갖는다. phase 는 실행단위로서 goal과 바인딩된다.

![maven_lifecycle-acf99801-30f0-475c-b462-f9997ee6f0ae](https://user-images.githubusercontent.com/33674947/57702602-6189b780-7699-11e9-9b1a-e4d3c3dbf3fa.png)

build default lifecycle

- clean : 빌드 시 생성되었던 산출물을 삭제
  1. pre-clean : clean 작업 전에 사전작업
  2. clean : 이전 빌드에서 생성된 모든 파일 삭제
  3. post-clean : 사후작업
- default : 프로젝트 배포절차, 패키지 타입별로 다르게 정의됌
  1. validate : 프로젝트 상태 점검, 빌드에 필요한 정보 존재유무 체크
  2. initialize : 빌드 상태를 초기화, 속성 설정, 작업 디렉터리 생성
  3. generate-sources : 컴파일에 필요한 소스 생성
  4. process-sources : 소스코드를 처리
  5. generate-resources : 패키지에 포함될 자원 생성
  6. compile : 프로젝트의 소스코드를 컴파일
  7. process-classes : 컴파일 후 후처리
  8. generate-test-source : 테스트를 위한 소스 코드를 생성
  9. process-test-source : 테스트 소스코드를 처리
  10. generate-test-resources : 테스팅을 위한 자원 생성
  11. process-test-resources : 테스트 대상 디렉터리에 자원을 복사하고 가공
  12. test-compile : 테스트 코드를 컴파일
  13. process-test-classes : 컴파일 후 후처리
  14. test : 단위 테스트 프레임워크를 이용해 테스트 수행
  15. prepare-package : 패키지 생성 전 사전작업
  16. package : 개발자가 선택한 war, jar 등의 패키징 수행
  17. pre-integration-test : 통합테스팅 전 사전작업
  18. integration-test : 통합테스트
  19. post-integration : 통합테스팅 후 사후작업
  20. verify : 패키지가 품질 기준에 적합한지 검사
  21. install : 패키지를 로컬 저장소에 설치
  22. deploy : 패키지를 원격 저장소에 배포
- site : 프로젝트 문서화 절차
  1. pre-site : 사전작업
  2. site : 사이트문서 생성
  3. post-site : 사후작업 및 배포 전 사전작업
  4. site-deploy : 생성된 문서를 웹 서버에 배포

<br>

---

### DEPENDENCY (POM.XML)

의존성에 대한 정보는 POM.XML에 저장된다. 이 정의에 의해서 MAVEN 은 자동으로 repository 를 검색하고 필요한 library 를 자동으로 추가해준다.

*마치 npm 의 package.json 같은 역할*

```xml
<dependency>
	<groupId>groupid</groupId>
	<artifactId>projectid</artifactId>
	<version>4.8.2</version>
	<scope>test</scope>
</dependency>
```

`<groupId>` : 프로젝트의 패키지 명칭

`<artifactId>` : artifact 이름, groupId 내에서 유일해야 한다.

```xml
<groupId>org.springframework</groupId>
<artifactId>spring-webmvc</artifactId>
```

`<version>` : artifact 의 현재버전 ex. 1.0-SNAPSHOT

`<name>` : 어플리케이션 명칭

`<packaging>` : 패키징 유형(jar, war 등)

`<distributionManagement>` : artifact가 배포될 저장소 정보와 설정

`<parent>` : 프로젝트의 계층 정보

`<dependencyManagement>` : 의존성 처리에 대한 기본 설정 영역

`<dependencies>` : 의존성 정의 영역

`<repositories>` : 이거 안쓰면 공식 maven 저장소를 활용하지만, 사용하면 거기 저장소를 사용

`<build>` : 빌드에 사용할 플러그인 목록을 나열

`<reporting>` : 리포팅에 사용할 플러그인 목록을 나열

`<properties>` : 보기좋게 관리가능, 보통 버전에 많이 쓴다.

```
<!-- properties 에 이렇게 추가하면 -->
  <spring-version>4.3.3.RELEASE</spring-version>

<!-- dependencies 에 이렇게 쓸수 있다. -->
  <version>${spring-version}</version>
```

<br>

*TEST STRUCTURE*

```xml
<modelVersion>4.0.0</modelVersion>

<!-- The Basics -->
<groupId>...</groupId>
<artifactId>...</artifactId>
<version>...</version>
<packaging>...</packaging>

<dependencies>...</dependencies>
<parent>...</parent>
<dependencyManagement>...</dependencyManagement>
<modules>...</modules>

<properties>...</properties>

<!-- Build Settings -->
<build>...</build>
<reporting>...</reporting>

<!-- More Project Information -->
<name>...</name>
<description>...</description>
<url>...</url>
<inceptionYear>...</inceptionYear>
<licenses>...</licenses>
<organization>...</organization>
<developers>...</developers>
<contributors>...</contributors>

<!-- Environment Settings -->
<issueManagement>...</issueManagement>
<ciManagement>...</ciManagement>
<mailingLists>...</mailingLists>
<scm>...</scm>
<prerequisites>...</prerequisites>
<repositories>...</repositories>
<pluginRepositories>...</pluginRepositories>
<distributionManagement>...</distributionManagement>
<profiles>...</profiles>
```

![pom_xml_example-b5060146-b06e-47eb-b2ac-0a6ed1c21c45](https://user-images.githubusercontent.com/33674947/57702603-6189b780-7699-11e9-9511-8af5e8f600ba.png)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.test.spark</groupId>
  <artifactId>testSparkApplication</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>wordCounting</name>
  <dependencies>
  	<dependency>
  		<groupId>org.apache.spark</groupId>
  		<artifactId>spark-core_2.11</artifactId>
  		<version>2.4.0</version>
  		<scope>provided</scope>
  	</dependency>
  	<dependency>
  		<groupId>org.apache.spark</groupId>
  		<artifactId>spark-sql_2.11</artifactId>
  		<version>2.4.0</version>
  		<scope>provided</scope>
  	</dependency>
  </dependencies>
  <build>
  	<pluginManagement>
  		<plugins>
  			<plugin>
  				<groupId>org.apache.maven.plugins</groupId>
  				<artifactId>maven.compiler-plugin</artifactId>
  				<version>3.1</version>
  				<configuration>
  					<source>1.8</source>
  					<target>1.8</target>
  				</configuration>
  			</plugin>
  		</plugins>
  	</pluginManagement>
  </build>
</project>
```

<br>

---

### BUILD

/src/main/java 에 패키지를 생성하고 예제 java 파일을 생성한다.

그리고 maven 을 빌드해서 tar 파일을 생성해보자

example '[wordcounting.java](http://wordcounting.java)'

![3-364fd36b-cc31-4064-a0ee-77848e972f84](https://user-images.githubusercontent.com/33674947/57702889-eb398500-7699-11e9-94b5-8da52fc63e02.png)

*maven build*

![4-9afccd89-38a2-44d2-afb6-9d9accda295b](https://user-images.githubusercontent.com/33674947/57702890-ebd21b80-7699-11e9-9c80-5792a5deb2d3.png)

*maven build configuration*

![5-be3ab936-7b11-467d-b272-1a976616d02a](https://user-images.githubusercontent.com/33674947/57702892-ebd21b80-7699-11e9-8126-6a08192740d3.png)

*build confirmation*

위와 같이 BUILD SUCCESS 가 나오면 성공이다.

![6-3caf4958-3e70-4549-8172-15dc72b21e8a](https://user-images.githubusercontent.com/33674947/57702894-ebd21b80-7699-11e9-99e7-d707d80f8b65.png)

<br>

---

### SPARK .tar FILE TESTING

서버의 spark 가 설치되어 있는 폴더로 이동하여

```
bin/spark-submit —class 클래스이름 jar파일 분석할md파일
```

![7-975cefcf-30c5-4bb7-864b-40d9103b28b2](https://user-images.githubusercontent.com/33674947/57702895-ebd21b80-7699-11e9-9bbe-8c994daef59d.png)

위와 같이 적으면 동작한다. 결과는 다음과 같다.

![9-2ab6da5d-903f-41b2-972e-a3d3f29ebd68](https://user-images.githubusercontent.com/33674947/57702896-ec6ab200-7699-11e9-9c27-8664443248d5.png)

<br>

---

### REFERENCE

[https://www.slideshare.net/KangDognhyun/2apache-spark](https://www.slideshare.net/KangDognhyun/2apache-spark)

{% reference https://www.slideshare.net/KangDognhyun/2apache-spark %}

<br>

---

### HOW TO SUBMIT APPLICATION

*Standalone Document*

[Spark Standalone Mode - Spark 2.4.0 Documentation](https://spark.apache.org/docs/latest/spark-standalone.html)

{% reference https://spark.apache.org/docs/latest/spark-standalone.html %}


<br>



Standalone 으로 cluster 를 실행한다

`./sbin/start-master.sh`

![10-40524bf1-6704-46b1-9815-27be15b1287d](https://user-images.githubusercontent.com/33674947/57702898-ec6ab200-7699-11e9-9ad4-e887194a1a25.png)

위와 같이 cluster 실행에 성공하면 기본적으로 [localhost:8080](http://localhost:8080) 에서 master 의 web ui 를 확인 할 수 있다.

![11-1c46299b-3cbe-4fcc-ad16-d9150cb2542f](https://user-images.githubusercontent.com/33674947/57702899-ec6ab200-7699-11e9-9f1b-60c830ea0e41.png)

아직 실행중인 worker 나 application 이 없는 것을 확인 할 수 있다.

위에서 만든 wordcounting application 을 배포하려면 spark가 설치된 폴더에서

`bin/spark-submit --master spark://computername:7077 --class javaclass something.jar somewhat.md`

위와 같이 실행한다. 이 명령은 실행된 cluster 에 application 을 연결하는데, `—master spark://computername:7077` 옵션으로 연결시킨다.

<br>

------

*Submitting Application Reference*

[Submitting Applications - Spark 2.4.0 Documentation](https://spark.apache.org/docs/latest/submitting-applications.html)

{% reference https://spark.apache.org/docs/latest/submitting-applications.html %}

Running application이 master 에서 인식된 모습

![spark_master_window1-169d17e3-ab21-4bc0-8ae3-e29754fa5a75](https://user-images.githubusercontent.com/33674947/57702605-6189b780-7699-11e9-8c38-a1c3748c448f.png)

Terminal에서 application 을 종료하였을 때 인식된 모습

![spark_master_window2-ebf0c6e4-d3d4-41a4-ac46-c15ace879f0c](https://user-images.githubusercontent.com/33674947/57702607-62224e00-7699-11e9-8815-ebc882318241.png)

Terminal 에서 application 을 종료하였을 때 인식된 모습

<br>

---

### HOW TO SET WORKERS

순서는 다음과 같다

1. 클러스터 할당
2. 클러스터에 워커 할당
3. 어플리케이션 시작

*Commands*

1. `./sbin/start-master.sh`
2. `./sbin/start-slave.sh <master-url:port>`
3. `bin/spark-submit --master <Spark-Master URL> --class <Package>.<Class> <Example>.jar`

*Real Code*

```
./sbin/start-master.sh
./sbin/start-slave.sh jungyu:7077
bin/spark-submit --master spark://jungyu:7077 --class testSparkApplication.wordCounting testcase/testSparkApplication-0.0.1-SNAPSHOT.jar testcase/test.md
```

할당된 worker 에는 default 값으로 core 와 memory 가 할당된다.

만약 이를 변경하고 싶다면 `/conf/spark-env.sh` 파일에 다음과 같은 줄을 추가한다.

```
export SPARK_WORKER_INSTANCES=2
export SPARK_WORKER_MEMORY=1024m
export SPARK_WORKER_CORES=4
```

*Worker 2개, 각각 4개의 Core 및 1G Memory 가 나타난다*

![12-4610d968-1879-4070-8bfd-49f769a3f4a4](https://user-images.githubusercontent.com/33674947/57702900-ed034880-7699-11e9-96b4-b9bf555edc7b.png)

![13-863eaf20-0309-4df6-8f7a-828877edfb8b](https://user-images.githubusercontent.com/33674947/57702903-ed034880-7699-11e9-9b15-9bde5eb0a77e.png)

<br>

---

### *LOG 숨기기*

터미널에서 stdout 을 확인해야 하는데, 자꾸 다른 로그들이 겹쳐 나와서 보기가 힘든 경우가 있다. 이 모든 출력되는 로그들은 `/conf/log4j.properties` 에  설정이 되어있다. 여기에서 `log4j.rootCategory=INFO, console` 부분을 `log4j.rootCategory=ERROR, console` 로 바꾸어 주면 ERROR 에 대한 로그만 출력되어 편리하다.

아래는 설정 영상이다.

<script id="asciicast-R3b1kV4sP3v0YhOnfpk9lO2eB" src="https://asciinema.org/a/R3b1kV4sP3v0YhOnfpk9lO2eB.js" async></script>
