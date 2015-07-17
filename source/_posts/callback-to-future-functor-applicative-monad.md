title: Callback에서 Future로(그리고 Functor, Monad)
date: 2015-05-28 09:39:49
tags: 
- translation
- monad
- functor
---

> ### Translation of "[From callback to (Future -> Functor -> Monad)](http://tech.pro/blog/6742/callback-to-future-functor-applicative-monad)" into Korean, under the same license as the original.


## 동기 

함수형 프로그래밍에서 기본개념은 **조합(composition)** 이다. 간단히 설명해서, 단순한 것들을 엮어서 더
복잡한 것을 만들 수 있고 그 결과를 다시 엮어서 더 복잡한 것을 만들 수도 있다. 함수의 의미나 리턴값이
무엇인지만 알고 있으면 조합으로 무엇이든 만들어낼 수 있다.

Node.js를 써봤으면 아래와 같은코드를 본 적이 있을 것이다.

``` js
fs.readFile('...', function (err, data) {
  if (err) throw err;
  ....
});
```

위의 코드는 전형적인 CPS(continuation-passing style) 함수이다. `fs.readFile`이라는 CPS
함수는 계속(continuation) 진행될 콜백을 추가 파라미터로 받는다. 이 CPS가 끝나면 호출한 곳에 값을
반환하는게 아니라 계속 함수, 콜백에 계산 결과를 넘겨준다.

나는 콜백을 쓰는걸 꺼리진 않는다. 사실 콜백은 부수효과를 표현하거나 이벤트 알림같은 것을 다룰 때
훌륭하다. 그렇지만 그걸로 흐름을 관리하기 시작하면 함정에 빠진 것이다. 왜냐면 조합할 수 없기 때문에.

자, 생각해보자. "위에 나온 함수의 **표기(denotation)**나 리턴값은 무슨 의미일까?" 답은
`undefined`이다. undefined라는건 테스트할 때 실제로 undefined인지 확인하는 용도 이외에는 쓸
데가 없다.

콜백 안에서 다른 실행 흐름으로 넘어갈 때 일방통행이라는게 문제다.

물리학에서 유명한 블랙홀처럼 콜백을 생각해보자:

> 블랙홀은 수학적으로 정의된 지역이다. 강한 인력이 작용하거나, 어떤 티끌이나 전자기적 파장조차
> 빠져나갈 수 없는

그 중에서도

> 어떤 빛도 반사하지 않는 등 블랙홀은 많은 부분에서 이상적인 검은 물체처럼 작용한다.

콜백 함수 또한 흐름에서의 어떤 것도 반사하지 못한다.

나중에 첫번째 콜백에 들어갈 때 다른 콜백 스타일 함수를 쓸 수 있는데, 그때는 두번째 **흐름**을 잃게
되고 다른 구멍에 빠지게 된다. 콜백을 쓰면 쓸 수록 지옥에 빠지게 된다.

그럼 블랙홀에 빠지지 않고 코드를 진행할 수는 없을까?

답은 **조합**이다. 하지만 조합을 사용하려면 일단 CPS 함수가 어디로도 돌아갈 수 없다는 사실을
알아야하고, 함수로부터 뭔가를 받아와야한다. 그러니 어떻게든 함수가 뭔가를 반환하게 만들어야한다. 어떤
값이 반환될까? 이게 이 글의 동기이다.

이미 자바스크립트에서의 해답을 알고 있을 수 있다. 하지만 계속 이 글을 읽도록, 강하게, 추천한다.
지시적인(즉 함수형) 생각의 힘을 보게 될 것이고, 깔끔하고 간결한 해답을 어떻게 사용할지 보게 될 것이다.

## future로 입문

파일 읽기, 네트워크 요청, DOM 이벤트, 이런 함수들의 공통점은 뭘까?

이 함수들은 _즉시_ 완료되지 않는 것들이다. 즉, (보통 함수들을 다루는 식으로는) 현재 프로그램
흐름에서 저 함수들이 완료될 때까지 기다릴 수 없다는 뜻이다. 그래서 _future_를 설명할 것이다.

그래서 특별한 반환 타입, 나중에 결과를 만들어준다고 명시하는 `Future`를 만들어보자. 요점은 다른
함수들로 넘길 수 있는 1등급 클래스 값을 사용하는 것이다.

Future는 무슨 의미일까? 특정 시간(0이 될 수도 있다) 후에 발생할 것이라고 명시해놓은 값이다. 
그 시간은 우리가 x초 후라고 말하는 것처럼 명시적인 시간이 될 수도 있지만, Future 2개가 완료된 후
혹은 Future 하나가 완료된 뒤 다른 Future 완료될 때처럼 상대적인 개념일 수도 있다.

여기서 중요한 점은: **Future의 결과는 항상 불변값이다.**

즉, 완료 값을 어떤 방법으로든 변경할 수 없다. 이 제약으로 구현 뿐만 아니라 의미론에 대한 추론도
간단해진다.

Future는 일회용의 간단한 상태머신처럼 구현될 수 있다. 이 머신은 _대기_ 로 시작했다가 _완료_ 가 된 
후에 멈춘다. 한번 완료되면 계속 완료상태에 고정된다.

내부적으로 `Future`는 콜백에 여전히 의존하고 있지만, 그 콜백들이 컨트롤 흐름 매커니즘을 방해하지는 
않는다. 대신 올바른 목적으로만 사용된다, 이벤트 알림.

``` js
function Future() {
  // 대기중인 구독들을 저장하는 리스트
  this.slots = [];
}

// 완료를 알린다
Future.prototype.ready = function(slot) {
  if(this.completed) slot(this.value);
  else this.slots.push(slot);
}

// 간단한 로그 유틸리티
function logF(f) {
  f.ready( v => console.log(v) );
}
```

Future를 완료시키는 외부 인터페이스로 메소드가 있다.

``` js
Future.prototype.complete = function(val) {
  // 불변성 보장
  if(this.completed)
    throw "이미 완료된 Future는 완료시킬 수 없다."

  this.value = val;
  this.completed = true;

  // 구독들에게 알림
  for(var i=0, len=this.slots.length; i< len; i++) {
    this.slots[i](val);
  }

  // 모두 실행되면 이제 필요없다.
  this.slots = null;
}
```

Future의 가장 간단한 예제로 어떤 값으로 _즉시_ 완료시켜보자. 그 역할을 `unit`이란 메소드를 
만들어보자.

``` js
// unit: Value -> Future<Value>
Future.unit = function(val) {
  var fut = new Future();
  fut.complete(val);
  return fut;
}

logF( Future.unit('hi now') );
```

코드에 대해 간단히 설명하기 위해 _타입 표기(type annotation)_ 를 사용했다.

`unit: Value -> Future<Value>`를 풀어보면 1- `unit`은 함수고, 2- 제네릭 타입 `Value`를 
입력으로 받으며, 3- 제너릭 타입을 가진 `Future` 인스턴스를 리턴한다. 여기서 타입 정보는 중요하지 
않으므로 `Value`라는 제너릭은 신경쓰지 않아도 된다.

다음 예제는 특정 시간이 지나고 완료되는 값이다.

``` js
// delay: (Value, Number) -> Future<Value>
Future.delay = function(v, millis) {
  var f = new Future();
  setTimeout(function() {
    f.complete(v);
  }, millis);
  return f;
}

logF( Future.delay('안녕, 이건 5초 걸린다', 5000) );
```

`delay`의 결과는 주어진 값만큼의 시간이 지난 뒤에 완료되는 Future다.

readFile 예제로 돌아가서, 이제 CPS 함수 대신에 Future를 리턴하는 함수를 사용할 수 있다.

``` js
var fs = require('fs');

// readFileF: (String, Object) -> Future<String>
function readFileF(file, options) {
  var f = new Future();
  fs.readFile(file, options, function(err, data) {
    // 에러는 잠시 후에 다루겠다

    if(err) throw err;
    f.complete(data);
  });
  return f;
}

logF( readFileF('test.txt', {encoding: 'utf8'}) );
```
`readFileF`의 결과는 인자로 받은 파일 이름의 내용을 잡고 있는 Future가 된다.

## Future 다루기: 첫번째 게스트

`Future`를 결과적으로 함수의 결과를 잡고 있는 마법 상자처럼 생각할 수도 있다.

뭔가 쓸모있는 것을 하려면 Future 타입에서 쓸모있는 연산들을 제공해야한다. 그러지 않는다면
필요없이 또 다른 `undefined`를 만든 것과 다를 바 없다.

그러면 어떤 연산을 Future에서 제공해야할까?

Future 상자에서 잡고 있는 값에 어떤 연산을 하고 싶을 때 (function map을 줄인)`fmap`을 호출할 
것이다.

`fmap`의 예제를 보자. 여기서 Future는 텍스트 파일의 내용을 잡고 있고, 이 내용의 길이를 계산하려고 
한다.

``` js
var textF = readFileF('test.txt', {encoding: 'utf8'});

// fmap: (Future<String, (String -> Number)> -> Future<Number>)
var lengthF = textF.fmap( text => text.length );
logF( lengthF );
```

`lengthF`의 뜻은 인자로 받은 Future가 잡고 있는 파일 내용의 길이를 잡고 있는 Future다.

일반화를 해보자면, `fmap`은 인자를 둘 받는데, 하나는 값을 잡고 있는 Future고 하나는 일반값을 
다루는 매핑 함수다. 입력으로 받은 Future의 결과물에 매핑 함수를 적용한 결과를 잡고 이는 Future가 
결과로 나온다. 받은 Future와 결과 Future는 둘 다 동시에 완료된다.

정확하진 않지만, 이렇게 표현할 수 있다

``` js
fmap( Future<value>, func ) = Future< func(value) >
```

`fmap`은 몇줄만으로 구현할 수 있다.

``` js
Future.prototype.fmap = function(fn) {
  var fut = new Future();
  this.ready(function(val) {
    fut.complete( fn(val) );
  });
  return fut;
}
```

현재 Future가 완료되었을 때 결과로 나온 Future도 완료된다. 그 때 매핑 함수를 적용시킨다.

위의 예제에서는, 파일 내용을 잡고 있는 Future를 내용 길이를 잡고 있는 다른 Future로 변이시켰다.

어디서 들어본 말 같지 않은가? 잘 알고 있는 자바스크립트 Array의 `map` 메소드와 꽤 비슷하다. 
실제로 정확히 같은 개념이다.

- Array  타입은 여러 값들을 잡고 있는 박스다
- Future 타입은 완료될 값을 잡고 있는 박스다
- Array.map(...) 은 Array 박스 안의 값들을 변이시켜서, 변이된 값들을 잡고 있는 다른 Array 박스를 돌려준다
- Future.fmap(...)은 Future 박스 안의 값을 변이시켜서, 변이된 값을 잡고 있는 다른 Future 박스를 돌려준다

Array와 Future 타입 둘 모두 포함되는 **Functor**라는 첫번째 게스트가 등장했다. 일반 함수를 하나 
받아서 안에 무엇을 가지고 있든 그것이 변이된 결과를 표현하는 다른 인스턴스를 만들어내는 타입이다.

- 다른 타입을 감싸는 컨텍스트처럼 작동할 수 있는 타입이고
- 내부에 있는 것을 일반 함수에 적용시킬 수 있다면

Array와 Future가 아니더라도 그게 무엇이든간에 그 타입을 **Functor**라고 부를 수 있다.

이제 Future를 다른 Future로 매핑할 수 있다. 이제 일반값을 다루듯이 Future를 직접적으로 다루는 
함수를 만들 수 있다는 뜻이다. `textF.fmap( c => c.length )`처럼 호출하는 대신에 Future를 
직접 다루는 `lengthF`라는 특별한 종류의 함수를 만들 수도 있다.

``` js
// lengthF: Future<String> -> Future<Number>
function lengthF(strF) {
  return strF.fmap( s => s.length )
}
```

파일 길이를 읽는 예제를 흔히 보던 방법처럼 다시 작성할 수 있게 되었다.

``` js
nbCharsF = lengthF( readFileF('...') )
```

`lengthF`를 _lift된_ 함수라고 부른다. Functor같은 _박스 타입_을 다루는 함수를 **lift**한다는 
것은 일반값을 다루는 함수를 박스 타입을 다루는 함수로 만든다는 뜻이다. 여기에서는 문자열을 다루는 함수 
`length(String)`를 lift해서 Future를 다루는 함수`lengthF( Future<String> )`로 lift했다.

일반화된 `lift1`(인자를 하나만 받아서 lift하는 함수)를 정의해보자.

``` js
Future.lift1 = function(fn) {
  return fut => fut.fmap(fn);
}
```

비동기 실행을 일반 함수 실행처럼 만들어주는 간단한 추상 함수다. 위에서 `lengthF( readFileF('...') )`는 
`readFileF`와 `lengthF`를 조합해서 비동기 연산을 현재의 흐름을 떠나지 않고 실행할 수 있다.

## 인자를 여러개 받는 함수는 어떻게? (두번째 게스트?)

질문에 대답하기 전에 잠시 기초지식에 대해 생각해보자: Future 박스가 잡을 수 있는 타입에는 뭐가 
있을까? Future는 모든 타입에 대해 같은 의미를 가질까?

`Future<String>`의 뜻은 명확하다: 시간이 지난 뒤에 문자열 타입의 값이 발생한다는 뜻이다. 다른 
타입들에 이 의미를 확장할 수 있을까? 숫자, 객체, 배열? 그럴듯... 그럼 Future 자체에 대해서는 
어떨까? `Future<Future>`는 무슨 뜻일까? 그러니까 Future의 Future는?

보고 바로 이해할 수 있도록, 디렉토리를 보고 첫번째 파일의 내용을 읽는 간단한 예제를 만들어보자
(간단히 생각하기 위해 내부에 다른 디렉토리가 없다고 가정한다).

Node.js에서는 비동기 함수 `fs.readdir`을 통해 디렉토리 속 파일들 이름의 배열을 가져올 수 있다. 
먼저 이걸 Future식 함수로 만들어보자.

``` js
// readDirF: String -> Future< Array<String> >
function readDirF(path) {
  var f = new Future();
  fs.readdir(path, (err, files) => {
    // 기다리면 곧 실행된다
    if(err) throw err;
    f.complete(files);
  });
  return f;
}
```

`readDirF`는 디렉토리 내 파일 이름들의 배열을 기다리는 Future를 뜻한다.

위에서 말한걸 구현하려면 필요한 나머지는

1. Future가 잡고 있는 파일 이름들의 배열을 기다린다.
2. 첫번째 파일명을 가져온다.

여기서 `fmap`을 사용할 수 있을까? Node에서 이걸 실행해보자

``` js
var resultF = readDirF("testdir").fmap( files => readFileF( files[0]) )
logF( resultF )
```

기다리면... 아차

``` js
{ slots: [] }
```

확실히 뭔가 잘못됐다. 콘솔에서 파일 내용이 나오는게 아니라 Future 인스턴스 객체의 내용이 나왔다.

왜냐면 `fmap`은 매핑 함수의 결과가 무엇이든 받아서 그걸 Future로 잡아 돌려주기 때문이다. 위에서의 
매핑 함수는 또다른 Future(`readFileF`의 결과)를 `fmap`은 그 Future를 잡는 Future를 만들어 
`resultF`에 보내기만 한다.

하지만 Future는 잡고 있는 Future와 함께 끝나는지 않으므로, _속에 있는_ Future가 완료될 때까지 
계속 기다릴 뿐이다.

그래서 이럴 때 필요한 함수를 만들어보자. Future를 리턴하고 끝내는 대신에 속에 있는 Future가 끝날 
때까지 기다리는 함수다.

(이중 Future)Future<Future>를 그냥 Future로 만들어주는 `flatten`를 만들어보자.

``` js
// flatten: Future< Future<Value> > -> Future<Value>
Future.prototype.flatten = function() {
  var fut = new Future();
  this.ready(function(fut2) {
    fut2.ready( function(val) {
      fut.complete(val);
    } );
  });
  return fut;
}
```

이렇게 하면 원하는 결과를 얻을 수 있다.

``` js
var result = readDirF("testdir")
              .fmap( files => readFileF(files[0], {encoding: 'utf8'}) )
logF( result.flatten() )
```

`fmap`과 `flatten`을 따로 부르는 대신에 한번에 부를 수 있게 합쳐보자: 매핑 함수에서 나온 2중 
Future를 압축(flatten)하는 두가지 일을 한다. 하는 일 그대로 `flatMap`이라고 하자
(좀 이상한건 나도 안다).

``` js
Future.prototype.flatMap = function( fn ) {
  return this.fmap(fn).flatten();
}
```

개념상으로는 위에서 독립적인 두 연산을 _이어서_하는 것인데, `readDirF`에서 나오는 파일 이름의 
배열을 `readFileF`에 넘겨준다.

여기에서 두번째 게스트가 등장하는데, Future를 Functor라고 부를 수 있는 것처럼 **Monad**라고 부를 
수도 있다. 순서대로 연산할 수 있는 방법에 대한 개념이다. 위에서 `flatMap`에서처럼, 이전 단계에서의 
결과를 다음 단계로 넘겨서 여러 함수를 연이어 연산할 수 있다.

Functor처럼 Monad도 많은 사용법이 있는데, 기술적으로 모든 모나드는 다음을 만족한다.

- 일반값을 _Monad식(Monoadic) 값_ 으로 lift하는 방법: 예를 들어, `Future.unit`은 일반값을 Future로 만든다.
- 연이은 연산 2개를 이어서 실행하는 방법: Monad는 연산을 이어서 실행하게 해주는 방법이 포함된다. 위에서 `flatMap`은 그냥 Future 하나만 만들고 다음으로 넘어가는게 아니라, 앞의 Future가 끝날 때까지 기다렸다가 넘어가는 방법이 들어있다.

위에서 2개의 다른 연산(`fmap`과 `flatten`)으로 두번째 인터페이스(`flatMap`)를 만들 수 있다는 
것을 확인했다. fmap 함수를 정의하는 Functor라면 이중 구조를 단순화시켜서 합치는(flatten) 연산이
필요해진다.

이제 처음의 질문으로 돌아가보자, Future들 여러개를 받는 함수를 어떻게 lift할 수 있을까?

다시 파일 예제로 돌아가서, 디렉토리의 모든 파일 내용을 합치려면 이런 코드가 될 것이다.

``` js
// concatF: (Future<String, ...) -> Future<String>
var resultF = concatF( text1F, text2F, ...)
```

이건 무슨 뜻일까? 입력받은 Future들이 잡고 있는 각 문자열들을 합친 것을 다시 잡고 있는 Future를 
만들어준다. `concatF`는 입력받은 모든 Future들이 순서대로 처리되도록 기다려야하므로 결과로 나온 
Future는 입력받은 모든 Future가 완료될 때 완료된다.

인자 2개를 받는 경우부터 시작해보자.


``` js
// fn: (Value, Value) -> Value
// lift2: ( (Value, Value) -> Value ) -> ( (Future, Future) -> Future )
Future.lift2 = function(fn) {
  return (fut1, fut2) => {
    fut1.flatMap( value1 => 
      fut2.flatMap( value2 =>
        Future.unit( fn(value1, value2) );
      )
    )
  };
}
```

보이는 것과는 별개로 코드의 로직은 꽤 간단하다. 한줄씩 읽어보자면:

- `Future.lift2`는 "일반값 2개를 다루는 함수"를 받아서 "Future 2개를 다루는 함수"를 리턴한다.
- 리턴된 (lift된) 함수가 실제로 하는 일은
  + (중첩되어 실행되는) 2개의 연산을 순서대로 `flatMap`에 넣고
  + 첫번째 연산은 그 자체로 하는게 없지만 `value1`을 바인딩해서 스코프에 묶어두는 역할을 하고
  + 두번째로 중첩된 연산은 `value1`과 `value2`를 `fn`에 넘긴다.
  + `fn`은 일반값을 리턴하는데 `flatMap`은 받은 함수가 Future를 리턴해야하므로 `Future.unit`을 통해 일반값을 Future로 lift한다.

이게 트릭이다: 모든 Future에서 순차적으로 `flatMap`을 실행해서 모두 끝나길 기다린 다음에 모든 
완료값이 한 스코프에 모였을 때 함수를 실행한다.

`readDir` 내부에서 `readFile`를 실행하는 것처럼 순차 연산으로 설명되는 _Monadic_ 값과는 다르게 
여러 인자를 한번에 lift하도록 마지막에 `Future.unit`를 사용했다.

파일 2개의 내용을 합치기 위한 예제다.

``` js
var concat2 = Future.lift2( (str1, str2) => str1+' '+str2 );

var text1 = readFileF('test1.txt', {encoding: 'utf8'});
var text2 = readFileF('test2.txt', {encoding: 'utf8'});

logF( concat2(text1, text2) );
```

두번째 Future `text2`가 첫번째의 `text1`보다 먼저 끝나더라도 `text1`을 기다리게 되고, 
`text1`이 끝나면 `text2`는 이미 끝났으므로 바로 함수를 실행한다.

여러 인자를 받는 함수는, 입력들이 언제 끝나는지나 의존성과는 관련없다는 것을 알 수 있다. 
이걸 정리하면 다음과 같다.

- `fmap`이 하나의 연산을 실행하고
- `flatMap`은 순차 연산을 실행하지만
- 여러 인자를 lift하는 함수는 _병렬_ 실행이다.

위에서 봤듯이, Future들을 한번에 실행하고 연산이 진행되기 전에 이미 그 결과를 기다리고 있다.

`lift2`의 패턴을 `lift3`이나 `lift4`로 쉽게 확장할 수 있지만, 인자의 갯수와 관계없이 위에서 
나온 중첩과 스코프를 통해 일반화를 구현해볼 것이다.

``` js
function toArray(args) {
  return Array.prototype.slice.call(args);
}

Future.lift = function(fn) {
  return function() {
    var futArgs = toArray(arguments), // Future 인자들
        ctx = this; // 컨텍스트(`this`)를 저장

    return bindArg(0, []);

    function bindArg(index, valArgs) {
      // 현재 Future 인자를 기다린다
      return futArgs[index].flatMap(function(val) {
        valArgs = valArgs.concat(val); // 완료값들을 모은다.

        return (idnex < futArgs.length - 1) ? // 아직 마지막 Future 인자가 아니라면
          bindArg(index+1, valArgs) : // 다음 인자를 flatMap에 넘기고 기다린다
          Future.unit( fn.apply(ctx, valArgs) ); // 끝까지 오면 모은 완료값들을 함수에 넘긴다
      });
    }
  }
}
```

`lift`에서는 `lift2`의 패턴을 재활용했다. 인자가 몇개 들어올지 정확히 모르니 재귀를 통해 전체를 
순회(iterate)하고, 완료를 기다렸다가 결과를 계속 넘겨서 모은다.(`index`번째의 Future를 
기다렸다가 완료값을 저장하고, 모든 입력이 완료될 때까지 다음 Future 입력에 이 연산을 반복한다.) 
마지막 Future까지 오면 함수를 실행하고 결과를 lift해서 리턴한다.

노트: 'Applicative Functor'라는 자료구조를 통해 n개 인자를 lift하도록 구현할 수 있지만, 
그러려면 람다나 커리에 대한 설명을 해야하므로 오늘은 일단 생략하자.


## 에러 처리

위에서 `fs.readFile`의 에러값을 어떻게 뒀는지 다시 확인해보자.

``` js
function readFileF(file, options) {
  var f = new Future();
  fs.readFile(file, options, function(err, data) {
    if(err) throw err;
    ...
```

실제로 작동하지 않는 코드다. 프로그램 흐름에서 떨어져서 실행중이므로 발생하는 에러를 잡을 방법이 없다. 
위의 상황에서 에러는 상위로 전파되며 잡는 핸들러가 없어서 Node.js 전체 프로그램을 중단시킨다.

에러를 잡아 흐름을 고치려고 한다거나 의미있는 메시지를 사용자에게 전달하는게 필요할 수도 있다.

가능한 방법으로는 `Future`에 _실패_ 의 개념을 붙여서 의미를 확장하는 것이 있다. 아직까지는 Future의 
결과에 어떤 의미를 붙이지는 않았지만, 가능한 2가지 결과(완료 혹은 실패)로 Future를 생각해볼 수도 있다. 
실패에 대한 경우가 포함되었는지 확인해보자.

먼저, 완료를 알리는 `ready` 메소드가 있으니 실패를 알리는 메소드를 정의해보자.

``` js
function Future() {
  this.slots = [];
  this.failslots = [];
}

Future.prototype.failed = function(slot) {
  if(this.hasFailed) slot(this.error);
  else this.failslots.push(slot);
}
```

Future가 실패할 때의 메소드도 정의해보자.

``` js
Future.prototype.fail = function(err) {
  if(this.completed || this.hasFailed)
    throw "이미 끝난 Future를 실패할 수는 없다!"
  this.hasFailed = true;
  this.error = err;
  for(var i=0, len=this.failslots.length ; i<len ; i++) {
    this.failslots[i](err);
  }
}
```

이제 `fmap`를 다시 생각해보자.

`readFileF(...).fmap( s => s.length)` 예제에서 파일이 없을 때에 대한 처리가 없다.
제대로 읽었을 때에 대해서만 변환하기 때문에 아닐 때는 에러와 함께 실패할 것이다. 혹시 변환 중 실패할
경우에도 실패해야한다.

``` js
Future.prototype.fmap = function(fn) {
  var fut = new Future();
  this.ready( val => {
    try        { fut.complete( fn(val) ); }
    catch(err) { fut.fail(err); }
  });
  this.failed( err => fut.fail(err) );

  return fut;
}
```

`flatten`은 약간 복잡하다. 안쪽과 바깥쪽의 Future 2개가 있고, 각각 완료될 수도 실패할 수도 있다.
그래서 4가지(2x2) 경우를 다뤄야한다.

``` js
Future.prototype.flatten = function() {
  var fut = new Future();

  // 1- 밖깥 실패 안쪽 실패 => 결과 실패
  // 2- 바깥 실패 안쪽 완료 => 결과 실패
  this.failed( _ => fut.fail(err) );

  // 3- 바깥 완료 안쪽 실패 => 결과 실패
  this.ready( fut2 =>
    fut2.failed( err => fut.fail(err) );
  );

  // 4- 바깥 완료 안쪽 완료 => 결과 완료
  this.ready( fut2 =>
    fut2.ready( val => fut.complete(val) );
  );

  return fut;
}
```

`flatten`에서 안쪽과 바깥쪽 모두 완료되었을 때만 결과가 완료된다.

`flatMap`과 `lift`는 수정할 필요가 없다. 이미 `fmap`과 `flatten`의 의미를 가져오는 것이기
때문에 자동으로 에러에 대한 의미가 추가된다.

자, 이제 실패한 Future들은 연산에서 제외하게 만들었다. 그럼 실패한 Future들을 어떻게 다뤄야할까?

Future 에러를 _잡아서_ 고치면 된다. 어떻게? 실패한 Future를 완료값으로 변이시켜서 원래의 연산에
포함시키면된다.

`fmap`과 비슷하지만 좌우반전같은 `fmapError` 함수를 만들 것이다.

``` js
Future.prototype.fmapError = funciton(fn) {
  var fut = new Future();
  this.ready( val => fut.complete(val) );
  this.failed( err => {
    try         { fut.complete( fn(err) ); }
    catch(err1) { fut.fail(err1); }
  });
  return fut;
}
```

`fmapError`는 `catch`문의 비동기 버전처럼 작동하며, 정상적으로 완료되면 그냥 값을 넘기고 에러가
발생했을 때는 매핑 함수에 적용시켜서 완료값으로 넘긴다.

간단히 예제를 만들어보자

``` js
readFileF('unknown file').fmapError( err => 'alternate content')
```

그럼 에러를 Monad식으로 파이프라인처럼 다음 연산으로 넘기려면?

`flatMap`의 좌우반전같은 `flatMapError`를 만들어보자

``` js
Future.prototype.flatMapError = funciton( fn ){
  return this.fmapError(fn).flatten();
}
```

예를 들어서 어떤 주소(URL)에서 내용을 가져오려고 할 때 요청이 실패한다면 다른 주소에서 가져오도록
시도를 하려고 하는데, `flatMapError`을 사용해서 앞의 실패를 잡아서 다른 요청을 만들 수 있다.

``` js
resultF = requestF('/url1').flatMapError( err => requestF('/url2') )
```

`resultF`는 첫번째 요청이 성공할 때 `'url1'`의 내용을 잡고 있고, 실패할 때는 `'url2'`를
요청해서 그 결과를 잡고 있다는 뜻이다.

## 부수효과

합성해서 연산할 수 있는 방법에 대해 필요한 것들을 모두 다뤄보았다. 지금까지 다뤘던 함수들을 통해서
Future를 동기 연산을 할 때처럼 일반값으로 넘겨서 비동기 처리를 하게 해봈다. 

하지만 연산들은 끝까지 도달해야 결과가 나온다. 부수효과가 필요한 연산들을 다뤄 볼 시간이다. UI를
업데이트한다거나 콘솔에 로그를 찍는다거나 데이터베이스에 저장을 한다거나.

`ready`와 `failed` 이벤트를 사용할 수도 있지만 좋은 방법은 아니라고 생각한다.

실제 어플리케이션에서 한 Future가 여러 자식 Future들을 가지고 그 Future들은 또 자식 Future들을
갖게 되는 트리같은 구조가 된다. Future하나가 완료돌 때 매핑된 Future들이 연쇄적으로 완료된다.

Future의 `ready`알림을 통해서 부수효과를 실행하려고 한다면 트리 내부에 있는 Future들 전체에
영향을 끼치게 된다. 의미적으로나 구현상으로나 업데이트가 끝날 때까지 부수효과 연산을 미뤄두는 것이 좋다.
예를 들어 DOM을 업데이트할 때는 `requestAnimationFrame`같은 스케쥴러에 맡기는게 더 좋을 수도 있다.

위에서 말한 이유로 `do`라는 메소드를 하나 만들텐데 부수효과 연산을 명시하는 것이다. `fmap`처럼
부수효과 함수를 받겠지만, 내부의 알림들(`ready`와 `failed`)이 완료될 때까지 지연될 것이다.

예를 들어

``` js
requestF('/url').do( val => /* update ... */ )
```

이번에도 _`do`의 의미와 리턴값이 무엇인지_ 생각해보자.

변이없이 그냥 Future를 리턴한다면 `future.fmap( Id )`(여기에서 `Id`는 `x => x` 같은 항등함수)
와 같은 형태이다. `fmap`과 다른 점은, 먼저 `do`에서 부수효과가 발생한다는 점이고 두번째는 다른
컨텍스트에서 실행된다는 점이다.(`fmap`은 즉시, `do`는 나중에). 가장 다른건 _의미_다.

> 정정: 2015년 4월 6일. `Action`이라는 새로운 타입을 통해 `do`를 적용했는데, 굳이 Monad(Future)
> 안에 다른 Monad(Action)을 넣어 복잡하게 만들 필요가 없었다. 서버에 데이터를 넘기거나 응답을 
> 기다리는 등의 상황에서 리턴값이 필요할 수도 있는데, 다음 글에 이걸 개발해 볼 수도 있다.

빠르게 대충 구현해보자.

``` js
Future.prototype.do = funciton(action) {
  var fut = new Future();
  if(this.completed) {
    action(this.value);
    fut.complete(this.value);
  } else {
    this.actions.push( val => { 
      action(val);
      fut.complete(val);
    });
  }

  return fut;
}

Future.prototype.complete = function(val) {
  ...
  var me = this;
  setTimeout( () => {
    for(var i=0, len=me.actions.length ; i<len; i++)
      me.actions[i](val);
  });
}
```

덧붙여서, 비동기실행을 제대로 구현하려면 process.nextTick이나 MessageChannel 등을 사용해야
하지만 여기서는 간단히 구현하고 넘어가자. 비슷하게, 부수효과의 실패에 대응해 `doError`도 만들어야
하는데, `do`와 비슷하므로 각자 알아서 구현해보자. ([Gist에 코드 전체가 있다](https://gist.github.com/yelouafi/40aeb2a70a368acb6e45))

---

역주 1: Promise와의 비교는 Future/Functor/Monad 개념을 이해하는데 관계없다고 생각해서 생략했다.

역주 2: [그림으로 설명하는 Functor, Applicative, Monad](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)([번역](http://netpyoung.github.io/external/functors_applicatives_and_monads_in_pictures/))과 함께 읽으면 이해하는데 도움이 될 것이다.
