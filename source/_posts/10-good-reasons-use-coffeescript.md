title: 커피스크립트를 쓰면 좋은 10가지 이유
date: 2012-09-19 18:32
tags: coffeescript
---

> ### Translation of "[10 good reasons to use CoffeeScript](http://www.netmagazine.com/features/10-good-reasons-use-coffeescript)" into Korean, under the same license as the original.

Caike souza는 CoffeeScript의 어떤 점들이 좋은지, 왜 이게 프로젝트에서 쓰일만한지에 대해 보여줄 것이다.

커피스크립트는 최근에 만들어진 인기있는 언어다. 커피스크립트는 자바스크립트의 강력한 객체 모델을 훨씬 간단한 문법으로 사용할 수 있을 뿐만 아니라 이미 존재하는 특징들 또한 쓸 수 있다. 여전히 커피스크립트가 배울만하다고 생각한다면, 내 대부분의 프로젝트에서 왜 쓰고 있는 10가지 이유들을 보자.


# 1. 깔끔한 문법

C언어의 문법에서 강하게 영향을 받은 자바스크립트처럼이 아니라 커피스크립트의 문법은 루비와 파이썬에서 영향을 받았다. 파이썬과 루비 코드를 본 적이 있으면 커피스크립트의 문법 몇개가 친숙할 것이다. 세미콜론으로 문장을 구분하지 않고 줄 단위로, 메소드의 호출이나 조건을 쓰는데 괄호는 옵션, 공백에 민감하고, 이 모든건 훨씬 쉽게 읽기 위한 문법이다.

``` coffee
message = 'Hello World'
sayHello message
```


# 2. 자바스크립트의 좋은 부분만 생성

자바스크립트에는 알려진 단점들이 있다. 커피스크립트는 항상 자바스크립트의 장점만을 생성하도록 최선을 다할 것이다. 한가지 예로 커피스크립트로 생성되는 모든 자바스크립트 파일은 자신의 영역(scope)를 가진다. 익명 함수로 코드를 감싸면 개발자가 이름이 충돌(naming collision)하지 않게 방지하고 전역 네임스페이스를 섞이지 않도록 해준다. 간단한 HelloWorld를 자바스크립트로 생성시킨건 다음과 같다.

```
(function() {
  var sayHello;  
  sayHello = function(message) {
    return console.log(message);
  };  
  sayHello('Hello World');
}).call(this);
```

위의 예시에서 `sayHello` 함수는 익명 함수 내에서만 가능하고 전역 네임스페이스에는 추가되지 않는다.




# 3. Fat Arrow

자바스크립트는 진짜 강력하고 유연한 언어다. 그러나 때론 이 특징들이 개발자가 이때문에 추가적인 코드를 쓰도록 요구한다. 커피스크립트에서는 언어 내부에서 이런걸 해결한다.

다음 예제에서 우리는 callback 함수의 내부에서부터 커피 객체의 context로 접근한 필요가 있다. 자바스크립트 개발자들 사이에서 널리 사용되는 규칙으로, callback 함수 외부에서 객체의 context 내부 변수로 값을 전달한다, 그래서 callback 함수로부터 참조가 되고 있어야한다, 다음 예시처럼

``` js
var coffee = {
  isFull: true,
  watchDrink: function(){
    var that = this;
    $('.drink a').on('click', function(){
      that.isFull = false;
    });
  }
};
```
이런 방법을 커피스크립트로 변환한다면 여전히 가능하다.

``` coffee
coffee =
  isFull: true
  watchDrink: ->
    that = this
    $('.drink a').on 'click', ->
      that.isFull = false
```

그러나 커피스크립트는 뚱뚱한 화살표 연산자(Fat Arrow Operation, 그냥 Fat Arrow는 원어로 표기해야겠다)로 똑같은걸 더 효과적으로 보여주는 방법이 있다. 이전의 커피스크립트 코드에서 이제 Fat Arrow를 쓰면 다음과 같다.

``` coffee
coffee =
  isFull: true
  watchDrink: ->
    $('.drink a').on 'click', =>
      @isFull = false
```

fat arrow를 사용하면, 커피의 context 안쪽으로 @ 연산자를 거쳐 callback 함수의 안쪽으로 참조할 수 있다. 커피스크립트에서 @는 `this` 키워드 대신이지만 => 안쪽에서만 사용가능하고, context 바깥의 this를 참조한다.



# 4. String interpolation

커피스크립트의 String interpolation은 루비처럼 작동한다. 자바스크립트에서처럼 +로 합치는 것 대신에 다음과 같이 겹따옴표 내부에서 #{} 안에 표현하면 된다.

``` coffee
name = 'John Doe'
console.log "Hi, my name is #{name}" 
```


# 5. List comprehensions

자바스크립트에서 엘리먼트 집합들을 하나씩 훑는 방법 중 가장 흔항 방법은 for 반복이다. 자바스크립트로 짠 for 반복의 간단한 예시다.

``` js
var names = ['Foo', 'Bar', 'Baz'];
for(var i=0; i < names.length; i++){
  console.log(names[i]);
}
```

파이썬 세계에서 잘 알려진 list comprehension을 사용해 배열을 순회할 수 있게 해준다.

``` coffee
names = ['Foo', 'Bar', 'Baz']
console.log(name) for name in names
```

자바스크립트의 for문과는 다르게, list comprehensions는 표현식이므로 반환해서 할당할 수도 있다.

``` coffee
names = ['Foo', 'Bar', 'Baz']
friends = (name for name in names when name != 'Bar')
```



# 6. Conditional modifier

기본적으로 if와 unless같은 조건식을 postfix 형태로 쓸 수 있다. 아래 코드를 읽어보면 그냥 영어 문장을 읽는 것같이 들릴 것이다.

``` coffee
allowEntrance() unless age < 21
allowEntrance() if age >= 21
```



# 7. the class keyword

자바스크립트는 prototype의 객체지향언어라서 클래스 선언에 대한 내부적인 지원은 없다. 그러나 커피스크립트는 지원한다. 컴파일러가 자바스크립트 코드로 적당히 표현되게 필요한 해석들을 잘 해준다. 커피스크립트에서의 클래스는 아래처럼 쓸 수 있다.

``` coffee
class Coffee  
  constructor: (@name) ->  
 
  brew: ->
    console.log 'Brewing'
 
  description: ->
    console.log "Coffee is #{@name}"
```

커피스크립트 클래스에서 객체를 만드는 방법은 자바스크립트에서 new 연산자를 통해 생성자 함수를 호출해서 만드는 방법과 완전히 똑같다.

``` coffee
frenchCoffee = new Coffee('French')
```

8. 전망

자바스크립트의 창시자인 Brendan Eich처럼 자바스크립트 커뮤니티의 주요한 사람들이 커피스크립트 문법 표준화를 하고 있다. class 키워드를 통한 prototype 상속 같은 것들, @ 연산자나 =>같은 것들은 실제로 몇년 안에 자바스크립트에 들어갈 것이다.

> "커피스크립트의 class, super, @같은 문법적인 좋은 것들로 prototype의 상속을 표준화시키려고 열심히 만들고 있다"
<p class="right">- 자바스크립트의 창시자, Brendan Eich</p>



# 9. 커뮤니티 전파

RoR 3.1 버전부터 커피스크립트를 out-of-the-box(바로 쓸 수 있게끔) 지원하기 시작했다. 파이썬이나 PHP같은 다른 언어의 Web Framework들은 컴파일 단계를 지원하는 서드파티 라이브러리들을 갖고 있다. 또 많은 유닛 테스트 프레임워크들이 커피스크립트를 지원하기에 커뮤니티들이 활발히 포용하고 있다.



# 10. 배우기 좋은 자원들

커피스크립트 공식 웹사이트는 훌륭한 문서와 바로 돌려볼 수 있는 예제들을 제공한다. 언어가 어떻게 만들어졌는지 자세하게 파볼 사람들을 위해 소스코드 또한 웹사이트에 잘 문서화되어있고 읽을 수 있다. 더 배울 수 있도록 많은 온라인 강좌, 책, 전자책, 블로그 포스팅들이 있다. CodeSchool.com은 입문할 수 있는 무료 커피스크립트 강좌가 있는 곳이다.


---


이것들 덕분에 커피스크립트에 더 관심을 갖게 되었다. 이 글이 커피스크립트가 배울 가치가 있는 언어인지에 대한 토론하는데 도움이 되길 바란다.


<style type="text/css">
.right {text-align: right;}
</style>