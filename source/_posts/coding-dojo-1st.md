title: "Coding Dojo #1 후기"
date: 2014-07-24
tags:
- swift
- osxdev
---

OSXDev에서 열린 [Coding Dojo](http://osxdev.org/forum/index.php?threads/swift-%EC%BD%94%EB%94%A9%EB%8F%84%EC%9E%A5-1%ED%9A%8C-%EA%B3%B5%EC%A7%80.367/)에 다녀왔다. 보통 Dojo가 붙은 사이트들을 생각해서 이해하지 못하면 어쩌나 긴장했는데 다행히 난이도는 예상보다 낮았다. 타겟은 "책을 읽었다"와 "이해했다" 사이의 사람이 대상인 것 같다. 그러니까 책은 읽었는데 어떻게 써야할지 감이 안오는 사람들을 위한 '체득'의 시간 정도? 문제를 풀어보고 해석하고의 반복으로 진행되어 네 문제를 풀었다.

진행했던 문제들과 소스보다는 어떤 의도의 문제들이었고 어떤 것을 얻었나를 정리해본다.

---

# 1. if문에서의 Optional과 Boolean의 차이
트위터에 예전에 올라왔던 문제인데 이 문제와 같은 의도였다.

<blockquote class="twitter-tweet" lang="ko"><p>Swift 퀴즈 - 아래 코드의 실행 결과는 무엇일까요? <a href="http://t.co/5wK9hEnILn">pic.twitter.com/5wK9hEnILn</a></p>&mdash; 김정현 (@gluebyte) <a href="https://twitter.com/gluebyte/statuses/487490260075417600">2014년 7월 11일</a></blockquote>
<script async src="http://platform.twitter.com/widgets.js" charset="utf-8"></script>

팁, 경우에 따라 다르겠지만 Boolean만 처리하지 말고 Optional에서 nil일 때도 처리해주는게 좋다.

# 2. switch에서의 pattern matching
복잡한 케이스에 대해서 switch-case를 단순히 objective-c에서 사용했던 것처럼 쓰거나 if-else문을 반복하는 것보다 tuple로 묶어서 한번에 처리하면 깔끔하고, where절(guard)을 통해 조건을 처리하면 더 깔끔하게 처리된다.

팁, pattern matching에서는 분기가 길어지면 case가 switch문과 거리가 멀어져서 어떤 의미인지 알기 어려운데, 특히 case에 true/false를 사용하는 경우 의미를 바로 알기 어려우므로 enum을 사용해서 의미있는 값을 변수로 만들어서 표현하면 좋다.

# 3. 간단한 알고리즘 풀이
중첩된 for문을 어떻게 사용하는지에 대한 문제. 참고로 100 doors 문제였다.

# 4. repeatString
String에 대한 기본 사용법(concat)과 loop 등을 사용한 정해진 횟수의 문자열 반복.

---

진도가 Control Flow까지만여서 그 안에서만으로 해결할 수 있는 문제들을 짧은 시간 내에 제출하느라 고생하셨으리라 생각된다.

덧 1, 4번 문제의 경우 제약 때문에 함수로 구현했지만 operator overloading을 통해 쓰기 좋게 만들 수도 있다.

```swift
func repeatString(count n:Int,string s:String) -> String {  
    return Array(count:n, repeatedValue:s).reduce("",+)
}

@infix func * (left: String, right: Int) -> String {
    return repeatString(count: right, string: left)
}

@infix func * (left: Int, right: String) -> String {
    return repeatString(count: left, string: right)
}

println("a" * 2) // "aa"  
println(2 * "a") // "aa"  
```
덧 2, swift 프로젝트에서 다른 파일에 구현을 해도 하나로 묶어서 해석하므로 같은 이름의 함수를 구현하면 에러가 난다는 질문이 있었는데, 얼마 전에 추가된 접근자(access control)을 통해 해결할 수 있을 줄 알고 시도해봤지만 안된다. c처럼 static 키워드가 생기거나 다른 방법을 찾아봐야겠다.

