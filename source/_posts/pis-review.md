title: 책 후기, Programming in Scala 2nd
date: 2015-01-18 05:31:17
tags: 
- review
- scala
---

> 이런 테크닉들을 익혔을 때의 좋은 부가효과는, 사용하는 **모든** 언어에서 더 좋은 개발자가 된다는 것이다. 나는 스칼라와 자바스크립트 모두에서 클로저나 다른 테크닉들을 익히는데 정말 도움이 됐고 더 좋은 자바스크립트 프로그래머가 되었다.
> 
> <p class="right">[스칼라로의 전환][transition]에서</p>

스칼라를 도전해본 것은 이번이 처음이 아니다. 함수형에 대해 이름만 알던 차에 [자바 개발자를 위한 함수형 프로그래밍][func-java]를 읽고 함수형이라는 개념을 더 배우고 싶어했고, 코세라(Coursera)에 가입하게 된 계기이자 처음으로(그리고 현재로서는 마지막으로) 수료한 [스칼라로 배우는 함수형 프로그래밍 기초][progfun]로 함수형이라는 패러다임을 잠깐 맛보았다. 그리고 1년 정도 손을 놓았다가 [리액티브 프로그래밍 기초][reactive]에 다시 도전했다가 영어 실력의 한계와 스칼라의 이해가 부족했다라는 점만 뼈저리게 깨닫고 중도포기를 했다. 그리고 [쉽게 배워서 빨리 써먹는 스칼라 프로그래밍][impatient]를 통해 문법은 리뷰할 수 있었지만 언어 자체와 함수형에 대한 이해도가 좋아지진 않았다. 

스칼라를 더 공부해보고 싶다는 막연한 생각은 가지고 있었고, 함수형에 대한 재미 때문에 underscore/lo-dash를 다양하게 써본다거나 [함수형 자바스크립트][func-js] 책을 재미있게 읽기도 했고, 파이썬을 다시 써볼까 싶어서 나간 스터디에서 [프로젝트 오일러][euler]의 문제를 최대한 함수형으로 풀어보면서 더 간결한 코드 작성이 가능해졌다는걸 느꼈다. 그러던 차에 내가 들었던 두 강의의 교수이자 스칼라를 만든 마틴 오더스키의 책, [스칼라 프로그래밍 2판][pis] 번역판이 출간된다는 이야기에 많이 기대하고 있었다. 책 내용과 관련없는 이야기를 길게 늘어놓은 이유는, 내가 생각하기에 나는 초심자와 다름없는 수준같지만 그래도 완전히 스칼라를 처음 접하는 사람과 느끼는 부분이 다를 수 있어서 겪어온 과정을 적어봤다.

---

첫번째 장에서는 스칼라의 특징들에 대한 설명과 어떤 언어에서 어떤 장점들을 가져온 것인지에 대해 나열하며 앞으로 배울 것들에 대해 개요를 보여준다. 하지만 언어론에 대해 관심이 있는 사람이 아니라면 적당히 훑어보고 나중에 다시 읽거나 아니면 구조적인 내용(10장 상속과 구성)이 나오기 전까지 읽고 나서 언어에 대한 감을 잡고 다시 1장을 읽어보기를 권한다. 1장을 (체감상)세 호흡 정도로 읽었다면 2장부터 9장까지는 거의 한 호흡에 집중해서 쭉 읽었다.

2장부터는 필요할 때 REPL로 한줄 정도 테스트해보면서 쭉 읽어나갔다. 그래도 문법에 대해서는 어느 정도 알고 있어서 무난히 넘어가긴 했지만, 이 책으로 처음 스칼라를 접한 사람들에게는 코딩을 시도해볼 여지가 별로 없다. 새로운 언어를 배울 때 이것저것 시도해보면서 체득하는 것이 중요하다고 생각해서 이 책의 그나마 단점이 있다면 그런 면이 아닐까 생각한다. 위에서 언급한 [쉽게 배워서~][impatient]의 경우에는 설명이 빈약해서 그 책만으로 스칼라를 이해하기 어렵지만 챕터마다 연습문제가 있어서 이 책을 읽으면서 같이 진행한다면 좋은 부교재가 되지 않을까 싶다.


### 추천 챕터

오랜만에 끝까지 정독한 책이지만 아직 완전히 이해했다고 생각하지 않아서 챕터들에 대해 평가를 내리기 조심스럽지만, 그래도 이 책을 읽을 사람들을 위해서 꼭 두번 읽고 넘어가라고 권해주고 싶은 챕터들이 있어서 메모를 해뒀다.

- 12.7 트레이트냐 아니냐, 이것이 문제로다: 어떨 때 트레이트로 만들어야하는지에 대한 가이드라인
- 15.1 패턴 매치(p322-324): 패턴매치가 어떻게 작동하는지에 대해 화이트박스로 설명하는 자세한 가이드
- 16.10 스칼라의 타입 추론 알고리즘 이해: 어떤 순서와 힌트로 타입을 추론하는지에 대한 매카니즘과 현재 불가능한 점.
- 22.1 List 클래스 개괄: 함수형 언어들에서 List(정확히는 cons 구조)가 왜 중요한지, 그리고 이 구조를 이해해야 지연 평가를 이해
- 24.15 뷰: Scala를 Scala답게 쓰기 위한 기능(자세히 설명하기 어렵지만 읽고 나면 이해될 것이다)


### 외부 라이브러리 사용

그리고 책에서는 `scala.actors.Actor`가 2.11부터 삭제예정(deprecated)이니 akka를 사용할 것을 권장하고 있는데, 2.11.0부터 아예 패키지 자체가 삭제되었다. 또, 2.11.0부터 독립한 다른 라이브러리들도 있다. [Scala 2.11.0][2.11.0] API 페이지를 보면 다음과 같은 부분이 있다.

- scala.reflect - Scala's reflection API (scala-reflect.jar)
- scala.xml - XML parsing, manipulation, and serialization (scala-xml.jar)
- scala.swing - A convenient wrapper around Java's GUI framework called Swing (scala-swing.jar)
- scala.util.continuations - Delimited continuations using continuation-passing-style (scala-continuations-library.jar, scala-continuations-plugin.jar)
- scala.util.parsing - Parser combinators, including an example implementation of a JSON parser (scala-parser-combinators.jar)
- scala.actors - Actor-based concurrency (deprecated and replaced by Akka actors, scala-actors.jar)

책을 기준으로 akka를 제외하고 28장 XML 다루기(scala-xml.jar), 33장 콤비네이터 파싱(scala-parser-combinators.jar), 34장 GUI 프로그래밍(scala-swing.jar)이 해당된다. 신기한 것은 REPL로 한줄씩 테스트해볼 때는 몰랐는데, scala-xml이 독립했다는걸 보고 이클립스에서 프로젝트로 만들어서 실험해봤더니 XML 표현법(literal)에서 에러가 났다. REPL(`scala`)의 경우, bash script 파일인데 여기서 기본 라이브러리(homebrew로 설치한 경우 `/usr/local/Cellar/scala/2.11.4/libexec/lib`)를 classpath로 추가해서 실행되므로 가능한 것이다.

외부 라이브러리를 어떻게 사용하는지에 대해 나와있지 않아서 sbt를 사용해보려고 검색하다가 이 책의 역자인 오현석님께서 번역하신 트위터의 [스칼라 학교][scalaschool]의 [빌드 도구 SBT(Simple Build Tool)][sbt] 챕터를 참고해 시도해보려고 했지만, 간단한 의존성 추가에 너무 많은 공수가 들어서 sbt 레퍼런스에서 [라이브러리 의존성][dependency]를 참고해 build.sbt을 작성했다. 예를 들어 akka-actor만 추가할 경우에

```
name := "Ciruit Simulator"

version := "0.1.0"

scalaVersion := "2.11.4"

libraryDependencies += "com.typesafe.akka" %% "akka-actor" % "2.3.8"
```

혹은 여러 라이브러리를 한번에 명시할 경우 `Seq`를 사용해서

```
name := "SCells Spreadsheet"

version := "0.1.0"

scalaVersion := "2.11.4"

libraryDependencies ++= Seq(
    "org.scala-lang" % "scala-swing" % "2.11+",
    "org.scala-lang.modules" %% "scala-parser-combinators" % "1.0.3"
)
```

이런 식으로 프로젝트 루트에 build.sbt파일을 만들고, `sbt update`를 통해 가져온 뒤 `sbt eclipse`를 통해 이클립스에 import할 수 있는 프로젝트를 생성할 수 있다.

### akka

책의 예제는 `scala.actors`가 기준이라 `akka.actor`로 예제 소스를 다시 쓰고 싶었지만 제대로 이해하지 못해서 도움이 될만한 글을 검색하던 도중 오현석님의 [프로그래밍 인 스칼라 액터 예제 akka로 변환하기(1) - 간단한 액터][akka-actor]를 발견해서 이 글과 [마이그레이션 가이드][migration]를 읽고 시도해보려다가 잘 안돼서 다시 검색하던 도중 누군가 만들어준 [프로젝트][akka-example]를 발견했다. 혹시 필요한 분들을 위해 남겨둔다.

---

책을 읽으면서 간략하게 메모해놓은 것을 토대로 짧은 후기와 함께 같은 고생을 할 분들을 위해 약간의 도움을 남기고자했는데 의외로 글이 길어졌다. 스칼라를 배우면서 좋았던건 처음에 언급했던 것처럼 함수형이라는 패러다임에 대한 이해에 도움이 된다는 것이었다. 설계부터 변경할 수 없는 값(immutable)이나 지연평가 등 함수형을 잘 살릴 수 있는 구조로 만들수도 있지만, 평소에 하던 스타일에 크게 변형을 주지 않으면서도 세세한 부분의 조금 더 좋게 개선할 수 있는 직교적(orthogonal)인 개념이라는 점에서 누구에게나 도움이 될 수 있지 않을까 생각한다. 다른 패러다임들이 무엇이 있을지 궁금해하던 차에 이 책을 읽는 도중에 발견했던 글이 있는데, AI의 대부격이라는 피터 노빅의 "프로그래밍 10년 완성"이라는 글에서 눈에 들어온 문단을 인용해본다.


> 프로그래밍을 정복하기 위한 나만의 비법이 있다:
> 
> - 최소 다섯가지 프로그래밍 언어를 배워라. class abstractions (자바 또는 C++) 지원하는 언어, coroutines을 (Icon 또는 Scheme) 지원하는 언어, functional abstraction (Lisp 또는 ML) 지원하는 언어, syntactic abstraction (Lisp) 지원하는 언어, declarative specifications를 (Prolog또는 C++ 템플렛) 지원하는 언어, 그리고 parallelism을 (Sisal) 지원하는 언어를 한개씩 배워라.
> 
> <p class="right">[프로그래밍 10년 완성][21days]([번역][21days-kr])에서</p>


---

##### 업데이트 01(2015-01-19 02:40)

[라 스칼라 코딩단][lascala]의 [슬랙 채널][lascala-slack]을 통해 오현석님의 조언을 받아, akka 부분과 REPL에서 scala-xml이 작동하는 이유에 대해 정정


<!-- reference and style -->
[transition]: https://medium.com/@kvnwbbr/transitioning-to-scala-d1818f25b2b7 "Transitioning to Scala"
[func-java]: http://www.hanbit.co.kr/ebook/look.html?isbn=9788979149678 "Functional Programming for Java Developers"
[progfun]: https://www.coursera.org/course/progfun "Functional Programming Principles in Scala"
[reactive]: https://www.coursera.org/course/reactive "Principles of Reactive Programming"
[impatient]: http://www.bjpublic.co.kr/skin12/product_list.php?boardT=v&page_idx=9&goods_data=aWR4PTk2JnN0YXJ0UGFnZT0zNiZsaXN0Tm89NjEmdGFibGU9cmVkX2dvb2RzJnBhZ2VfaWR4PTkmc2VhcmNoX2l0ZW09|| "Scala for the Impatient"
[pis]: http://www.acornpub.co.kr/book/programming-in-scala "Programming in Scala"
[21days]: http://www.norvig.com/21-days.html "Teach Yourself Programming in Ten Years"
[21days-kr]: http://blog.magicboy.net/entry/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-10%EB%85%84-%EC%99%84%EC%84%B1
[func-js]: http://hanbit.co.kr/book/look.html?isbn=978-89-6848-079-9 "Functional Javascript"
[euler]: http://euler.synap.co.kr/ "Project Euler"
[2.11.0]: http://www.scala-lang.org/api/2.11.0 "Scala Standard Library 2.11.0"
[scalaschool]: https://twitter.github.io/scala_school/ko/ "Scala School"
[sbt]: https://twitter.github.io/scala_school/ko/sbt.html "Simple Build Tool"
[dependency]: http://www.scala-sbt.org/0.13/tutorial/Library-Dependencies.html "Library dependencies"
[akka-actor]: http://www.enshahar.me/2014/07/akka.html?view=classic
[migration]: http://docs.scala-lang.org/overviews/core/actors-migration-guide.html "The Scala Actors Migration Guide"
[akka-example]: https://github.com/drozzy/parallel-discrete-event-akka
[lascala]: https://groups.google.com/forum/#!forum/scala-korea
[lascala-slack]: https://lascala.slack.com/


<style type="text/css">
.right { text-align: right; }
</style>
