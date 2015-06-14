title: Spark로 빅데이터 입문, 1-2주차 노트
date: 2015-06-10 21:18:51
tags:
- spark
- bigdata
---

edX에서 [Spark로 빅데이터 입문(Introduction to Big Data with Apache Spark)](https://courses.edx.org/courses/BerkeleyX/CS100.1x/1T2015)을
듣고 있다. UC Berkeley의 Anthony Joseph 교수가 진행하는 수업으로, 실제 데이터를 가지고 과제
4개를 진행하면서 Spark로 빅데이터 분석하는 방법을 배운다고 하는데, 수업 난이도 자체는 높지 않다.
대상은 Python 경험자로 분산 컴퓨팅/Spark에 대한 지식은 없어도 된다고 되어있다. 환경 설정도
Jupyter(IPython Notebook의 새 이름)와 PySpark가 이미 세팅된 환경을 Vagrant로 제공해주는데,
Vagrant의 이름만 알고 있는 정도였지만 동영상에서 OS별로 제공하는 동영상을 보고 따라하는데 무리는 없었다.

실습은 노트북 파일에서 비어있는 부분을 채우면서 진행하면 되는데, 의도에 대한 설명은 코드 위에 충분히
자세하게 되어있고 단계별 진행이다보니 정확히 읽지 않아도 코드를 보면서 따라가면 유추가 가능하다.
단순히 유행어(Buzzword) 이상으로 Big Data나 Spark에 대해 배워보고 싶은 사람들에게 추천할 겸,
몇달 뒤에 잊어먹을 나 자신을 위해서 기록을 남겨본다.


# 1주차

환경설정: Jupyer + PySpark가 세팅된 vagrant 올리기

수업 목표
- Data Science를 배워보자
- 실제 데이터를 다루는 과정
- Spark(w/ mllib)를 써보자

### Lecture 1. Big Data와 Data Science 소개

- 데이터 분석의 역사
- 원인 ≠ 상관관계
- 충분한 데이터가 필요
- 현재를 분석하는건 쉽고 미래를 예측하는건 어렵다
- 사건에 대한 중요한 요인을 모두 알고 있어야한다
- (MySpcae 사례를 통한 페이스북 유행시기 추정 -> 실패)

빅데이터는 어디에서?
- 사용자들이 올리는 컨텐츠들(웹/모바일) - 페이스북, 인스타그램, 옐프
- 생체정보/과학쪽 연산

그래프 데이터
- 많은 데이터들이 그래프 구조
- SNS, 네트워크 등

사람이 보기에 너무 많은 정보
- 아파치 서버 로그
- IoT의 센서 측정기록

빅데이터를 어떻게 다룰까
- Crowdsourcing
- Physical modeling
- Sensing
- Data Assimilation

많은 사람들에게서 정보를 얻어 분석한 다음에 중요한 정보를 시각화


### Lecture 2. Data Science는 어떻게? 데이터 준비하기

- 코딩 + 도메인 지식만으로는 잘못된 분석의 가능성
- Data Science = 코딩 + 도메인 + 통계지식

[The Data Science Venn Diagram](http://drewconway.com/zia/2013/3/26/the-data-science-venn-diagram)

Database와 Data Science의 차이
- 데이터가 중요하다 / 싸다
- 원자성이 중요 / 결과적으로 유지만되면 오케이
- 과거를 조회 / 미래를 예측
- 정해진 복잡도 / 하다보면 됨

방법
- Jim Gray 모델 vs Ben Fry 모델 vs Jeff Hammerbacher 모델 

어려움
- 실제 데이터는 너무 더러움
- 이전에 발견한 패턴/가설이 선입견

준비: ETL
- extract: 데이터 추출
- transform: 가공
- load: 모아놓는다



# 2주차

### Lecture 3. 빅 데이터, Spark

왜 필요한가?
- 페이스북의 하루 로그는 60TB
- 이제 머신 하나에서 다룰 수 없다

거대한 문서에서 단어를 세는 예제
- 머신 n-to-1
  + 문단 별 단어 카운트는 n개에서 나눠서 해도 된다
  + 합치는 작업을 1개에서 한다면 병목
- 머신 n-to-n
  + 합치는 작업도 단어별로 n개에서 나눠서
  + 이게 Map/Reduce 개념
- 문제
  + 머신끼리 데이터 보내는건 느리다
  + 하나가 실패하면? -> 그 머신만 다시 시작
  + 하나가 느리면? -> 하나 더 돌리고 먼저 끝난걸 선택
- 문제 2
  + 작업을 반복해서할 때 디스크에 저장하면 I/O가 병목
  + 메모리에 올려서 작업하면? -> Spark

Spark
+ 위에서 말한 실패하거나 느린 노드를 자동으로 처리
+ Core와 컴포넌트 4개 Spark SQL, Spark Streaming, MLlib, GraphX로 구성
+ disk에 넣기 위해 serialization/deserialization 할 필요가 없으므로 100배 이상 빠르다

특징
+ 다양한 경우에 쓸 수 있는 엔진
+ 리니지(immutable을 이용한 이력관리)의 lazy eval

### Lecture 4. Spark 기초

- 어플리케이션(driver)에서 SparkContext로 시작
- worker는 클러스터 혹은 로컬 스레드
- 분산된 worker의 추상화가 RDD

맨 처음 실행하면
- SparkContext 객체의 인스턴스 `sc` 생성
- `sc`로 로컬의 worker 접근, 혹은 hdfs, mesos 접근

RDD는
- 만들어지면 불변
- transform되는 과정(lineage)을 추적해서 재사용 가능
- 파티션을 설정하면 그만큼 병렬처리

연산은
- transformation, action 두 종류
- transform: lazy eval, action까지 미뤄둠
- persis(cache)는 메모리/디스크 저장

``` python
data = [1, 2, 3, 4, 5]
rDD = sc.parallelize(data, 4)
```

혹은

``` python
file = sc.textFile("README.md", 4)
```

transformation
- map, filter, distinct, flatMap, 

action
- 바로 계산
- reduce, take, collect, takeOrdered

``` python
lines = sc.textFile("...", 4)
comments = lines.filter(isComment)
print lines.count(), comments.count()
```

lines 연산을 한번, lines+comments 연산을 한번해서 중복. 그럴 때는 중간에 `lines.cache()`

key-value transformation
- reduceByKy, sortByKe, groupByKey
- groupByKey는 데이터가 이동하므로 네트워크/대량에서는 주의

``` python
rdd // [(1,2), (3,4), (3,6)]
rdd.reduceByKey(lambda a, b: a + b) // [(1,2), (3,10)]
```

closure
- broadcase: driver가 쓰면 worker들이 읽기 가능
- accumulator: worker들이 쓰기만 가능 driver만 접근가능



# Lab 1. Spark 과제

구텐베르크 프로젝트에서 셰익스피어 전집의 데이터로 단어 세기

- 특수문자 제거
- 띄어쓰기로 단어 구분
- 중복 제거를 위해 소문자로 변환
- 최빈 15개 단어


---

참고로 강의노트(PDF)가 공개되어있다.

- [Lecture 1 slides](https://courses.edx.org/c4x/BerkeleyX/CS100.1x/asset/Week1Lec1.pdf)
- [Lecture 2 slides](https://courses.edx.org/c4x/BerkeleyX/CS100.1x/asset/Week1Lec2.pdf)
- [Lecture 3 slides](https://courses.edx.org/c4x/BerkeleyX/CS100.1x/asset/Week2Lec3.pdf)
- [Lecture 4 slides](https://courses.edx.org/c4x/BerkeleyX/CS100.1x/asset/Week2Lec4.pdf)
