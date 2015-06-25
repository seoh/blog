title: Spark로 빅데이터 입문, 4주차 노트
date: 2015-06-26 03:24:00
tags:
- bigdata
- spark
---

# 4주차. 데이터 품질, 탐헌적 데이터 분석과 머신 러닝

### Lecture 7. 데이터 품질

데이터 클리닝

- 왜곡: 처리과정에서 변질된 표본들
- 선택편견: 값에 따른 표본의 가능도(likelihood)
- 좌우검열: 데이터가 무한대일 때 시작과 끝을 어떻게 자를지
- 의존성: 표본이 독립적인지 아닌지에 대한 판단

+ 정확성과 (과정의)간소에 대한 트레이드오프
+ 단위 통일, 중복 제거 등

문제
- 텍스트 파싱
- 같은 엔티티 다른 표현(2 vs two, NYC vs NewYork)
- 비구조적-구조적 전환시 primary key
- 너무 길어서 잘리는 필드
- 형식 문제(특히 날짜)

수집
- 과정에서 무결성 체크
- 구조에 없는건 기본값

전송
- 신뢰할만한 프로토콜인가
- 받은 데이터의 확인이 가능한가(checksum)

분석의 어려움
- 크기, 성능
- 모델에 적용
- 전문지식 부족
- 다트판(때려맞추기)
- 대충 경험(특정 상황에만 맞는 분석)

품질 측정
- 스키마 일치
- 정확성, 접근성, 해석가능
- Lab2에서 정규식을 통한 형식 일치 확인

용어?
- 개체 식별(entity resolution)
- 중복 검출(DeDup: Detection Duplicated)

표준화
- USPS에서 제공하는 [주소 표준가이드](http://pe.usps.com/text/pub28/welcome.htm)
- 다른 필드 참고 등 식별 힌트


### Lecture 8. 탐험적 데이터 분석과 머신 러닝

기술통계 vs 추론통계([위키피디아](https://ko.wikipedia.org/wiki/%ED%86%B5%EA%B3%84%ED%95%99#.EC.B6.94.EB.A1.A0_.ED.86.B5.EA.B3.84))

업무에서의 목적
- 간단한 통계
- 가설 검증
- 분류
- 예측

[탐험적 데이터분석](http://www.amazon.com/dp/0201076160)
- 기본 테크닉 소개
  + [Five-number summary](https://en.wikipedia.org/wiki/Five-number_summary)
  + box plot, stem and leaf diagram
- 통계요약의 문제: 같은 요약이라도 다른 데이터일 수 있다

정규 분포
- 평균, 표준편차
- 중심극한정리(Central Limit Theorem): n이 무한대로 가면 정규분포에 가까워진다.

다른 중요한 분포
- [프아송 분포](https://ko.wikipedia.org/wiki/%ED%91%B8%EC%95%84%EC%86%A1_%EB%B6%84%ED%8F%AC)
- 이항 분포, 다항 분포

Spark의 mllib
- NumPy와 함께 사용가능(pySpark >= 0.9)
- 여기에서는 영화평점 예측
  - collaborative filtering
  - k rank = user(a) x movie feature(b)


### Lab 3. 텍스트 분석과 개체 식별

1. 텍스트 유사성으로 개체 식별 - Bags of Words
  + [Bag of Words 기법](http://darkpgmr.tistory.com/125)
2. 텍스트 유사성으로 개체 식별- TF-IDF를 사용한 가중치 적용된 BOW
  + [TF-IDF](https://ko.wikipedia.org/wiki/TF-IDF)
3. 텍스트 유사성으로 개체 식별- 코사인 유사도(Cosine Similarity)
4. 역참조(inverted index)를 통한 효율적인 개체 식별
5. 그래프(plot)을 통한 결과 분석

### Lab 3. 퀴즈

Lab 3에서 배운 것들 재확인


---

[pySpark Docset](https://github.com/Kapeli/Dash-User-Contributions/tree/master/docsets/pyspark)
- [Dash](https://kapeli.com/dash)용 pySpark API문서
- 설정의 다운로드 -> 좌하단의 사용자 제공(User Contibuted) -> pySpark 검색

---

- [Lecture 7 slides](https://courses.edx.org/c4x/BerkeleyX/CS100.1x/asset/Week4Lec7.pdf)
- [Lecture 8 slides](https://courses.edx.org/c4x/BerkeleyX/CS100.1x/asset/Week4Lec8.pdf)


