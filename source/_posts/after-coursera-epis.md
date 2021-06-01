title: Coursera - EPiS 후기
date: 2021-06-01 22:44:34
tags:
- coursera
- Scala
---

Scala 3(a.k.a Dotty)의 업데이트와 함께 새로운 스칼라 입문 코스, [Effective Programming in Scala](https://www.coursera.org/learn/effective-scala)가 코세라에 올라왔다. [소개 영상](https://www.youtube.com/watch?v=MSDJ7ehjrqo)에 의하면 전제조건은 다른 프로그래밍 언어의 경험이 어느 정도 있을 것, 목표는 스칼라로 업무가 가능한 정도까지이다.  스칼라 입문이지 프로그래밍 입문이 아닌만큼 기본 개념에 대한 설명은 생략하고 다른 언어들에서 쓰이는 개념들은 스칼라에서 어떻게 쓰는지, 함수형으로는 어떻게 같은 논리를 구현하는지에 대해 초점이 맞춰있고 스칼라2에서는 어떻게 썼는지에 대해 차이점도 소개한다. 수업을 들으면서 정리를 좀 남기긴 했지만 스칼라 문법에 대한 이야기를 굳이 요약하기보다 수업을 따라 좋은 설명을 듣기를 추천하고 스칼라 2사용자들에게 유용한 내용들만 추려보겠다.

## 변경점
### indent-based syntax - 1주차
일단 가장 큰 변화는 들여쓰기 문법을 도입하면서 중괄호(`{}`)를 쓸 필요가 없어졌다는 것이다. 1주차 수업부터 조건문을 설명하면서
``` scala
if condition then
  expression
else
  expression
```
처럼 여러 줄의 표현식이 있을 때 중괄호없이 표기할 수 있게 되었다.


### imperative-loop - 2주차
지금까지 for문(`for comprehension`)을 쓸 때, flatMap, map, withFilter 등으로 변환된다고 알고 있었는데 여기에 foreach로 변환되는 문법이 하나 추가되었다. `for … do`인데
``` scala
for
  x <- exp1
do f(x)
// is equivalent to
exp1.foreach(x => f(x))
```
`yield`처럼 값을 반환하지 않고 실행만 하는 명령식 반복문(imperative loop)이 생겼다.

### package-object - 3주차
탑레벨(top-level) 변수들이 허용되어서 굳이 패키지 객체가 필요없긴 하지만 기능도 사라졌다.

### imports - 3주차
`import` 문에서 몇가지 변화가 생겼다. 일단은 패키지 내의 멤버 전부를 가져오는게 `import root.from.to._`였는데 이제는 `import root.from.to.*`로  `*`을 사용하게 되었다. 아직 하위호환으로 `_`도 사용할 수 있다.

그리고 이름을 변경하여 가져올 때 `import from.to.{Pkg => P}`였다면 이제는 `as`라는 키워드를 사용해 `import from.to.{Pkg as P}`처럼 쓰면 된다.

새로운 `given` 키워드와 새로운 문법상 맥락이 생기며 `given` 변수들은 `.*`로 가져올 수 없으니 given을 한번에 가져오려면 `import from.to.given`을 쓰거나 given을 포함한 다른 멤버들도 한번에 가져오려면 `import from.to{given, *}`처럼 사용하면 된다.

### Program Entry Point - 3주차
예전에는 Java처럼 `main(args: Array[String])` 메소드가 있는 Object들을 진입점들로 찾았다면 이제는 `@main`이 붙은 메소드들이 모두 진입점이 될 수 있다. 그리고 인자로 `@main def run(name: String, n: Int)`같은 식의 타입을 받으면 받은 인자들을 순서대로 저 타입 변환을 하는데 맞지않으면 실행이 되지 않는다. 


### Opaque Types - 3주차
예전에도 데이터의 일관성을 위해 타입을 다른 이름으로 바꾸거나 다른 타입의 인자에 넣거나 trait를 붙여서 구분하는 등의 방식들이 존재했는데, 그 중에서 실행 시점에서 추가적인 리소스를 소모하지 않는 방법은 type alias가 있었지만 원래 타입으로 변환이 가능해서 `type UserID = Long`, `type GroupID = Long`이면 두 타입을 혼용하거나 원래 타입과 구분할 수 없다는 단점이 있었고 이걸 해결하기 위해 opaque type이라는 기능이 도입되었다.

``` scala
object UserID:
  opaque type UserID = Long
  def parse(string: String) = string.toLongOption
  extension (id: UserID)
    def value(id: UserId): Long = id

def find(id: UserID): Option[User] =
  ... (id.value)
```
이렇게 타입에 이름을 붙이고, 원 타입과의 변환은 선언된 스코프 내에서만 가능해서 type alias와 다르게 안전하게 사용할 수 있다.

### Extension Method - 3주차
위의 예제에서 `id.value`로 `value` 멤버가 없는 opaque type에 접근한 것처럼 타입을 확장할 수 있는 기능이다. 예전에는 암묵적 변환(implicit conversion)을 통해 다른 클래스로 변환하고 그 클래스의 메소드를 실행하는 방식이었는데, `import`를 통해 extension을 가져올 수 있고 특수한 경우로 `UserID`처럼 opaque type에 연결되어있으면 그 opaque type만 import하면 가져올 수 있다.

### Given - 5주차
예전에도 Context Bound라는 타입 연산자(`:`)가 있었고, 풀어서 쓰자면
``` scala
def g[A : B](a: A)
// is equivelent to
def g[A](a: A)(implicit ev: B[A])
```
처럼 맥락에 해당하는 묵시적 변수를 `implicit`으로 표기했는데 이제는 모든 묵시적 행동에 쓰이던 implicit이라는 키워드가 사라지고 이런 용도로는 `using`으로 쓴다. 그리고 using에서 자동으로 가져오기 위한 변수를 선언하는 키워드는 `given`이 되었다.

``` scala
object Ordering:
  given IntOrd: Ordering[Int] with
    def compare(x: Int, y: Int) = ...
  given Ordering[Double] with
    def compare(x: Double, y: Double) = ...
```
이렇게 해당 given에 이름을 붙일 수도 생략할 수도 있고, `given intOrdering: Ordering[Int] = IntOdering`처럼 given이 아닌 변수지만 메소드를 제공한다면 given 변수에 할당해서 사용할 수도 있다.

또한 `implicitly`라고 문맥상 스코프에 존재하는 묵시적인 변수를 가져오는 함수는 이제 `summon`으로 쓴다. `summon[Ordering[Int]]`처럼 부르는데, 이것도 `implicitly`처럼 미리 선언된 함수이다.

given의 경우 다음과 같은 방식으로 가져올 수 있다
- 이름:  `import Ordering.Int`
- 타입:  `import Ordering.{given Ordering[Int]}`
- 타입*: `import Ordering.{given Ordering[?]}`
- 전부:  `import Ordering.given`

T 타입의 given은 다음과 같은 순서로 찾는다
1. 접근할 수 있는 given 인스턴스들
  - 상속받았거나 import했거나 스코프 안에서 정의된 변수들
2. T와 관련된 컴패니언 객체를 통해서
  - ‘관련된’의 의미는
    - T 자체의 컴패니언 객체
    - T가 상속하는 타입들의 컴패니언 객체
    - T에 있는 타입 인자들의 컴패니언 객체
    - T가 inner class라면 바깥쪽 스코프의 객체

`given a: A`가 `given b: B`보다 더 구체적이다, 라는 말은
   - a가 b보다 가까운 스코프에 있다
   - b가 정의된 클래스의 스버클래스 안에. a가 있다
   - a가 b의 서브타입이다
   - A 타입이 B 타입보다 더 “고정된” 부분이 있다.
     - `Ordering[Int]`가 `Ordering[?]`보다 더 고정되어 있다.


## 유용한 내용
sbt에 대한 설명은 아주 기본적인 것이나 아주 깊은 내용 아니면 찾기 어려워서 기본적으로 내부에서 사용하는 개념에 대해 정리된 자료를 찾기 힘든데, 3주차의 “sbt, Keys, and Scopes” 챕터에서 sbt 내에서 쓰이는 중요한 개념인 `Key`와 `Task`, 그리고 `Scope`에 대해 잘 설명해준다.