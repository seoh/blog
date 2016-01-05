title: Spark로 빅데이터 입문, 3주차 노트
date: 2015-06-14 23:43:28
tags:
- spark
- bigdata
---

### Lecture 5. 반구조적 데이터

자료 형태
- 구조적: 정형(schema) 데이터. RDB, formatted msg
- 반구조적: schema를 그때그때. XML, JSON, mp3tag
- 비구조적: plain text

파일이란
- byte의 나열
- FS를 통한 상하구조
- POSIX interface(이건 왜?)

테이블
- 처음부터 구조를 잘 짜야
- 같은 데이터도 타입문제(2 vs 2.0)
- 이력관리

취합 문제
+ 필드가 다를 때
+ 데이터 단위가 다름
+ 같은 값인데 표현이 다름

pandas
- data analysys + modeling for python
- DataFrame: named column
- R도 비슷한 data frame 지원

DF in pySpark
- 1.3부터 RDD 확장으로 지원
- pandas, R의 DF와 같지만 분산환경
- pandas DF와 convert 쉬움
- pandas -> pySpark시 driver 메모리 꽉 찰 수 있음 주의
- RDD는 Scala구현체가 Python구현체보다 두 배 이상 빠름
- DF는 RDD Scala보다 두 배쯤 빠르고 Py/Scala 비슷

Apache Common Log Format의 데이터를 분석해보자
- 컨텐츠 통계: status code, size
- 404 횟수
+ 하루에 단일host는 얼마나?
+ 하루에 요청은 얼마나?
+ 호스트당 평균 요청은?
+ 하루에 404는?

로그 마이닝
- splunk를 통해 event log, disk error, network, cpu/memory usage 등 통계 및 분석

read/write
- binary가 항상 빠르다
- 압축 알고리즘과 rw속도는 어느 정도 trade-off


### Lecture 6. 구조적 데이터 

RDB
- relation: schema + instance
- sparse에 약하다

SQL 기초
- select, (inner, outer)join
- join in Spark

``` py
x = sc.parallelize([("a", 1), ("b", 4)])
y = sc.parallelize([("a", 2), ("a", 3)])
x.join(y).collect
// [('a', (1, 2)), ('a', (1, 3))]

x = sc.parallelize([("a", 1), ("b", 4)])
y = sc.parallelize([("a", 2)])
x.leftOuterJoin(y).collect()
// [('a', (1, 2)), ('b', (4, None))]

x.rightOuterJoin(y).collect()
// [('a', (1, 2))]

x = sc.parallelize([("a", 1), ("b", 4)])
y = sc.parallelize([("a", 2), ("c", 8)])
x.fullOuterJoin(y).collect()
// [('a', (1, 2)), ('c', (None, 8)), ('b', (4, None))]
```

### Lab 2. 로그 분석

Apache Log Format을
1. 정규식으로 나눠서
2. 종류별 aggregation
3. key, value로 나눠서 plot
4. 그리기 위해 2, 3번의 반복이 많아 분량에 비해 생각할꺼리는 적음
5. 아마도 python 사용에 익숙치 않은 사람들을 위한 단순반복으로 추정


tip.

저번 과제에 비해 데이터가 커서연산할 때 시간이 많이 걸리므로 REPL이나
편한 python 툴을 꺼내놓고 기다리면서 다음 셀에서 어떻게 하면 될지 간단히
테스트해보면 좋다.

![](/images/big-data-with-spark-3-week/ptpython.png)

---

- [Lecture 5 slides](https://courses.edx.org/c4x/BerkeleyX/CS100.1x/asset/Week3Lec5.pdf)
- [Lecture 6 slides](https://courses.edx.org/c4x/BerkeleyX/CS100.1x/asset/Week3Lec6.pdf)



