title: "Coding Dojo #2 후기"
date: 2014-08-09
tags:
- swift
- osxdev
---

저번에 이은 [Coding Dojo](http://osxdev.org/forum/index.php?threads/8-6-swift-%EC%BD%94%EB%94%A9%EB%8F%84%EC%9E%A5-2%ED%9A%8C-%EA%B3%B5%EC%A7%80.373/)에 다녀왔다. 이번 범위는 함수에서 클로저까지.

# 1. 스코프 내의 변수 잡기

> 클로저는 사용자의 코드 안에서 전달되거나 사용할 수 있는 기능을 포함한 독립적인 블록(block)입니다. Swift에서의 클로저는 C 및 Objective-C 의 blocks와 유사하며, 다른 언어의 람다(lambda)와도 유사합니다. 클로저는 자신이 정의된 컨텍스트(context)로부터 임의의 상수 및 변수의 참조(reference)를 획득(capture)하고 저장할 수 있습니다.
> 
> [09 클로저 (Closures) by inureyes](http://seoh.github.io/Swift-Korean/#09-closures-)

for-loop(`for var...`)의 스코프에 있는 변수를 사용하는 함수를 만드는데, 해당하는 변수의 값이 스코프 내에서 계속 변하는 경우에 loop가 끝난 뒤 당연히 마지막값을 기준으로 함수가 실행되는 상황에서 현재값을 저장할 수 있는 방법에 대한 문제였다.

해결책은

1. 변수를 다시 캡처한다. `var _i = i`
2. for-loop를 돌 때 `for var...`가 아니라 for-in으로 돌면 상수`let`가 되어 따로 캡처할 필요가 없다.
3. 값을 받아서 그 값을 캡처하는 함수를 리턴하는 함수를 만든다.

```swift
func capture(i:Int, closure:Int->Int) -> ()->Int {  
    func sth() -> Int {
        return closure(i)
    }
    return sth
}
// capture(i){ $0*$0 }
```

3번의 경우를 더 간단하게 만들기 위해서 JavaScript의 즉시실행함수(IIFE)를 흉내내고 싶었는데, [Swift의 버그](http://stackoverflow.com/questions/25163311/type-inference-of-iife-in-swift) 때문에 현재 단순히 값만 리턴하는 IIFE도 `var`는 안되고 `let`을 써야 타입 추론이 가능하며, let을 쓰더라도 함수를 리턴하는 경우에도 에러가 나고, IIFE 속에서 조건문(conditional statement)이 존재할 경우 경우의 수에 대한 superset을 추론하는게 아니라 그냥 에러가 난다.

# 2. 부분함수와 합성함수의 단계적 구현

### 2.1. 기본 구현
Swift에서도 함수형 프로그래밍 스타일을 지원하도록 Array에 filter, map, reduce 등의 메소드들이 존재해서 array.filter{}.map{}.reduce(){} 같은 스타일로 작성할 수 있다. 그래서 특정 조건까지의 배열을 뽑아서(`take`) filter와 map을 체이닝해서 변이(tramsform)된 배열을 구하는 문제부터 시작했다. 앞/뒤에서 특정 길이만큼 읽는 함수는 Swift의 기본 라이브러리에서 `prefix와` `suffix`를 통해 지원하고 함수형 프로그래밍에서는 보통 `take`/`takeRight라는` 이름으로 지원된다. 특정 조건까지 읽는 함수는 `takeWhile`이라고 보통 부르는데, Swift에서 구현되어있지 않아서 도장에서는 [ExSwift](https://github.com/pNre/ExSwift/#instance-methods)에 있는 `takeWhile`의 소스가 제공되었다.

### 2.2. Pythonic solution
Python, underscore.js처럼 filter/map/reduce라는 함수에 sequence를 인자로 넘기는 식으로 개발을 하다보면 체이닝이 아니라 reduce(map(filter(... 처럼 전역함수를 이용해 구현할 수도 있는데, 이럴 경우에는 실행되는 순서와 함수호출의 순서가 역순이고, 값을 평가하는 함수를 인자로 넘길 때 인자로 받는 전역함수와의 거리가 멀어져서 가독성이 떨어지는 문제가 생긴다. 이 문제를 부분 함수(partial function)와 합성 함수(compose function)을 이용해서 인자를 받는 순서를 바꾸면 훨씬 가독성이 올라간다.

### 2.3. Functional Programming
함수형 프로그래밍으로 구현하기 위해서는 일단 1급 시민(first-class citizen, 혹은 1급 함수나 1급 객체 등으로 불린다)이라는 개념에 대해 이해가 먼저 필요하다. Java의 경험이 있다면 [함수형 언어로 가는 길 (중편) - 일급객체](http://blog.doortts.com/135), JavaScript의 경험이 있다면 [Javascript : 함수(function) 다시 보기](http://www.nextree.co.kr/p4150/)라는 자세한 설명의 글들이 있고 영문으로는 더 자세하고 풍부한 자료들이 있다. 그리고 함수형 프로그래밍에 대한 패러다임의 이해도 필요한데, 가장 좋은 방법은 함수형 프로그래밍을 지원하는 언어를 배우는 것이다. Twitter에서 만든 Scala School이라는 Scala 입문 강의가 있는데 [한글 번역](http://twitter.github.io/scala_school/ko)도 존재한다. 혹은 Java 경험이 있다면 [자바 개발자를 위한 함수형 프로그래밍](http://www.hanbit.co.kr/ebook/look.html?isbn=9788979149678)라는 eBook을, JavaScript 경험이 있다면 [함수형 자바스크립트](http://www.hanbit.co.kr/book/look.html?isbn=978-89-6848-079-9)라는 좋은 책들도 있다.

### 2.4. Solution
다시 도장 이야기로 돌아가서, 이 문제를 해결하기 위해 영후님은 F#이라는 언어의 pipe-forwarding(`|>`)이라는 연산자를 구현하셨다. [Swift에서의 pipe-forwarding |>의 구현](http://undefinedvalue.com/2014/07/13/fs-pipe-forward-operator-swift)이라는 글을 읽어보면 구현체와 그 설명이 자세히 나와있다.

도장에서 나온 문제와 일치하지는 않지만 위에 나온 과정들을 종합해보면 이런 예제를 만들 수 있다.

```swift
infix operator |>   { precedence 50 associativity left }

public func |> <T,U>(lhs: T, rhs: T -> U) -> U {  
    return rhs(lhs)
}

func ifilter<T>(closure: T->Bool) -> [T]->[T] {  
    return { filter($0, closure) }
}

func imap<T,S>(closure: T->S) -> [T]->[S] {  
    return { map($0, closure) }
}


let list = [1,2,3,4,5]  
         |> ifilter  { $0%2 == 0 }
         |> imap     { $0*2 }
         |> imap     { String($0) }

println(list) // ["4", "8"]  
```

# 3. Accumulator
누산기(accumulator)는 함수 하나를 리턴하는데, 그 함수는 인자를 하나 받을 때마다 그 값들이 누산된 결과를 리턴한다. 클로저에서 값을 캡처해서 리턴하는 것은 1번 문제에서 했고, 캡처된 값을 변경하도록 하는 것과 파라미터로 정의된 값을 다시 변수로 사용해서 소스코드를 짧게 만들도록 구현하는게 목적이었다.

# 4. Jensen's Device
이번에도 알고리즘 문제가 하나 나왔다. [Jensen's Device](http://en.wikipedia.org/wiki/Jensen's_Device)라는 문제로 한 변수를 캡처하고 있는 클로저를 계속 이용해서 따로 변수 선언없이 파라미터만으로 문제를 해결하도록하는 문제였다. 인자로 넘길 때 스코프 명시(`{}`)없이 쓸 수 있도록 해주는 `@auto_closure`라는 키워드에 대해 배웠다.

# 5. Conclusion
1회의 난이도가 1이었다면 이번 난이도는 10쯤 된다. 다음의 난이도는 얼마나 될지 모르겠다.

<style type="text/css">
blockquote p:first-child { text-align: initial !important; }
blockquote p:last-child { text-align: right; }
</style>