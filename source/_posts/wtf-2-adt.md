title: WTF - 2. 대수 자료형 (Algebraic Data Type)
date: 2015-08-04 22:57:53
tags:
- functional
- algebraic data type
- maybe

---

> What is the Functional?
>   1. [Introduction](/2015/08/04/wtf-1-intro/)
>   2. [Algebraic Data Type](/2015/08/04/wtf-2-adt/)
>   3. [Maybe or Not](/2015/08/04/wtf-3-fam/)
>   4. [Monadic Molecule Parser](/2015/08/04/wtf-4-parser/)

> 컴퓨터 프로그래밍에서, 특히 함수형 프로그래밍과 타입 이론에서 대수 자료형은
합성 타입의 한 종류이며 즉, 다른 타입들을 모아서 형성된 타입이다. 제일 보편적인
두 경우로는 프로덕트 타입(product type. 예를 들어 튜플과 레코드)과 섬 타입(sum
type. 혹은 Tagged Union이라 불린다)이 있다.
(중략)
대수 자료형의 값들을 패턴 매칭을 통해 분석해서 생성자나 필드 이름을 통해 값을
알아내거나 내부의 값을 추출해낸다. 대수 자료형은 70년대 에든버러 대학에서 개발한
작은 함수형 언어인 Hope에서 소개됐다.
>
> [Algebraic data type - Wikipedia](https://en.wikipedia.org/wiki/Algebraic_data_type)

프로덕트 타입은 여러 타입을 한 번에 쓸 수 있는 구조(튜플의 경우 (A, B), 레코드의
경우 {A: B}같은 형식)이며 섬 타입은 열거형(Union) 비슷한 이름을 갖는 것에서도
추정할 수 있듯 여러 타입 중 하나를 쓸 수 있는 구조다. 여러 언어에서 예전부터
혹은 최근에 도입된 자료형 중에서 Maybe, Option, Optional 등의 이름으로 값을
갖거나(Just) 혹은 값이 없거나(Nothing, None, Null)로 구분되는 자료형이 가장
익숙하지 않을까 싶다. 설명을 하다 보니 OOP에서 추상 클래스와 상속받은 클래스들간
메소드 오버로딩을 통한 다형성과 비슷한 느낌이 든다.

위키피디아의 내용처럼 패턴 매칭에서 주로 사용되는데 패턴 매칭을 지원하지 않는
JavaScript에서는 적용할 수 없는 개념이고 제대로 사용할 수 없으므로 이해하기
어려운 개념이기도 하다. 그런데 굳이 처음부터 잘 이해하고 갈 필요가 있나? 그냥
어떤 것인지 알아보고 JavaScript로 한번 구현해보면 제대로는 아니어도 어떤 것인지
정도는 감을 잡을 수 있을 것이다.

---

대수적 자료형의 재미있는 점은 재귀적 구조와 지연 평가(Lazy Evaluation)을 통해
무한의 자료형이 가능하다는 점인데, Codewars에서 Nat(0 이상의 정수)과 Cons를
JavaScript로 구현하는 문제가 있다.

Nat
---


``` haskell Haskell
data Nat = Zero | Succ Nat
```

Haskell에서 대수 자료형 Nat을 정의해보았다. 코드를 정확히 이해할 필요는 없고,
`Zero`라는 0에 해당하는 값과 Nat을 인자로 받아 그다음 값을 갖는 `Succ`의 두가지
경우가 될 수 있는 Nat이라는 섬 타입이다. 문제에서는 `Zero`와 `Succ`에 대해서
정확히 같지는 않지만, 다음과 비슷하게 주어진다.

``` js JavaScript
const zero = () => {};
const succ = (nat) => ( ()=>nat );
```

`zero`는 일종의 상수고, `succ`는 nat을 리턴하는 함수를 리턴한다. 즉, 패턴 매칭을
통해 Succ에서 받은 이전 값을 다시 추출하는 과정을 함수 호출로 대신했다.  패턴
매칭을 통해 Nat을 정숫값으로 변환하는 과정도 JavaScript로 대신해보자.

``` haskell Haskell
toInt :: Nat -> Int
toInt Zero = 0
toInt (Succ nat) = 1 + toInt nat
```

인자로 `Zero`를 받을 경우에는 당연히 0이 되고, `Succ`를 받았을 경우에 `Succ`를
생성할 때 받았던 이전 값 `nat`을 패턴 매칭을 통해 추출해서 다시 `toInt`로 넘겨서
Nat은 하나씩 이전값을 보고 `Zero`가 나올 때까지 결괏값을 하나씩 증가시킨다.

``` haskell Haskell
toInt (Succ (Succ (Succ Zero)))
```

이렇게 0의 세 번째 다음 값을 정수로 변환한다면 그 과정을 간단히 표현해서
`1 + (1 + (1 + 0)`이 되고 `3`을 리턴하게 된다. 그렇다면 이걸 JavaScript로
구현하면

``` js JavaScript
const toInt = (nat) =>
  nat === zero
    ? 0
    : 1 + toInt(nat())

console.log(toInt(succ(succ(succ(zero))))); // 3
```

이런 식으로 재귀를 통해 구현할 수 있다. 혹은 앞에서 언급했던 트램펄린과 비슷하게

``` js JavaScript
const toInt = (nat) => {
  let count = 0;
  for(; nat !== zero ; nat = nat(), count++);
  return count;
}

console.log(toInt(succ(succ(succ(zero))))); // 3
```

구현할 수도 있다. `succ`와 `zero`의 의미, 그리고 어떻게 다루어야하는지 방법을
알았으니 나머지 인터페이스는 쉽게 구현할 수 있다.

Cons
---

Codewars에 대수 자료형을 다룬 문제가 하나 더 있다. Nat처럼 재귀적인 구조로 아주
비슷한 Cons라는 구조다. 많은 언어에서 List 혹은 Sequence라는 이름으로 구현체를
제공하는 유명한 자료형이다.


``` haskell Haskell
data List a = Cons { head :: a , tail :: List a}
            | Nil
```

List는 `Cons` 혹은 `Nil`이 될 수 있는 섬 타입이다. 그리고 `Cons`는 다시 `a`
타입의 `head`와 `List a` 타입의 `tail`로 이루어진다. 위에서 말했듯이 Nat와
유사한 모양의 재귀적인 구조다. 참고로 `head`와 `tail`은 Cons 구조에서 일반적으로
쓰이는 이름이지만 언어에 따라서 [car과 cdr](https://en.wikipedia.org/wiki/CAR_and_CDR)로
표현하기도 한다. 또한, List와 Array를 모두 제공하는 언어나 라이브러리에서는 보통
C언어에서처럼 고정된 길이의 index를 가지는 배열을 Array(혹은 Vector)라고 부르고
앞서 말했듯 이런 Cons 구조를 List(Sequence)라고 부르는 경우가 많다.

``` js JavaScript
function Cons(head, tail) {
  this.head = head;
  this.tail = tail;
}
const Nil = new Cons(null, ()=> {
  throw new Error('Empty!');
});
```

문제를 다른 사람이 낸 것인지 앞에서는 함수 호출을 통해 지연 평가를 비슷하게
구현했는데 이번에는 간단하게 속성(property)으로만 구현해서 제공된다. 앞에서의
Nat과 비슷한 정수의 스트림을 구현해보면

``` haskell Haskell
int :: Int -> List Int
int n = Cons n tail
  where tail = int (n + 1)
```

이런 식이 된다. `tail`을 바로 평가하지 않기 때문에 `int 0`으로 List를 만들어도
바로 `Cons 0 tail`을 만들 뿐 무한루프를 돌지 않는다. JavaScript에서 비슷하게
만들어보자면

``` js JavaScript
const int = (n) => new Cons(n, ()=>int(n+1))

int(0)        // { head: 0, tail: [Function] }
int(0).tail() // { head: 1, tail: [Function] }
```
이렇게 계속 `tail`을 실행할 때마다 다음 `Cons`를 생성해서 지연 평가를 구현했다.
사실 링크의 문제에서는 지연 평가에 대한 내용이 없어서 굳이 이렇게까지 할 필요는
없지만, 문제풀이가 목적이 아니니. 그럼 언어가 제공하는 List에서 앞서 정의한
List(Cons|Nil)로 변환을 통해 Cons를 생성할 수 있도록 해보자.


``` haskell Haskell
fromArray :: [a] -> List a
fromArray [] = Nil
fromArray (x:xs) = Cons x (fromArray xs)
```

`x:xs`는 Cons의 `x`가 head고 `xs`가 tail이며 두 개를 연결해서 하나의 Cons를
만든다는 연산자 `:`이다. 참고로 `++`의 경우 두 개의 Cons를 연결(concat)한다.
이걸 또 JavaScript로 구현해보면 다음과 같다.

``` js JavaScript
const fromArray = (arr = []) => {
  if(arr.length === 0) return Nil;
  else return new Cons(arr.shift(), ()=>fromArray(arr.slice()));
}

let cons = fromArray([1, 2]);
console.log(cons); // { head: 1, tail: [Function] }
cons = cons.tail();
console.log(cons); // { head: 2, tail: [Function] }
cons = cons.tail();
console.log(cons, cons === Nil); // { head: null, tail: [Function] } true
cons = cons.tail();
//   throw new Error('Empty!');
//         ^
// Error: Empty!
```

이제 Sequence 타입에 항상 적용해보는 filter와 map을 구현해보자

``` haskell Haskell
filter' :: (a -> Bool) -> List a -> List a
filter' f Nil = Nil
filter' f (Cons x xs)
  | f x       = Cons x (filter' f xs)
  | otherwise = (filter' f xs)


map' :: (a -> b) -> List a -> List b
map' f Nil = Nil
map' f (Cons x xs) = Cons (f x) (map' f xs)
```

``` js JavaScript
const filter = (f, cons) => {
  if(cons === Nil) return Nil;
  else if(!f(cons.head)) return filter(f, cons.tail());
  else return new Cons(cons.head, ()=>filter(f, cons.tail()));
}

const map = (f, cons) => {
  if(cons === Nil) return Nil;
  else return new Cons(f(cons.head), ()=>map(f, cons.tail()));
}

const prt = (cons) => {
  while(cons !== Nil) {
    console.log(cons.head);
    cons = cons.tail();
  }
}
```

출력이 귀찮아서 `prt` 함수를 따로 만들었다. 이제 확인을 해보면

``` js JavaScript
const even = (d) => d % 2 === 0;
const doub = (d) => d * 2;
const toFive = fromArray([1,2,3,4,5]);

prt(filter(even, toFive));
// 2
// 4
prt(map(doub, toFive));
// 2
// 4
// 6
// 8
// 10
```

이제 재귀적인 구조를 다룰 때 어떻게 실행되는지에 대해 안에서 어떻게 돌아갈지를
간단하게 구현해봤는데 적당히 감이 왔을 것이다. 여기에서 약간의 스킬을 더해서
`window.setImmediate`나 `process.nextTick`으로 스레드 점유를 살짝 연기시키면
충분히 큰 자료도 시간만 있으면 다룰 수 있을 것이다. JavaScript에서 `map`과
`filter`를 어떻게 쓰는지 모르는 사람은 거의 없을 것이고 Cons에서 구현한 것들도
배열에서 쓰이는 것과 큰 차이가 없었다. 다음에는 이걸 대수적으로 해석할 때의
의미와 그걸 지원하는 구조에 대한 명칭을 이야기할 것이다.

---

Reference

- Codewars Kata
  + [Algebraic Data Types](http://www.codewars.com/kata/algebraic-data-types)
    : Nat = Zero | Succ Nat
  + [Algebraic List](http://www.codewars.com/kata/algebraic-lists)
    : List a = Cons {a, List a} | Nil
- 함수프로그래밍 실천기술(가칭), 제이펍 2015
