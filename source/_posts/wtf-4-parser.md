title: WTF - 4. 모나드 분자식 파서(Monadic Molecule Parser)
date: 2015-08-04 22:58:10
tags:
- functional
- monad

---

> What is the Functional?
>   1. [Introduction](/2015/08/04/wtf-1-intro/)
>   2. [Algebraic Data Type](/2015/08/04/wtf-2-adt/)
>   3. [Maybe or Not](/2015/08/04/wtf-3-fam/)
>   4. [Monadic Molecule Parser](/2015/08/04/wtf-4-parser/)

Codewars에 분자식(문자열)에서 원자들이 몇 개인지 세는 문제가 있었다. 이미
제출한 답은 다음과 같은 구조였다.

``` js JavaScript
const token = (str) => str.match(regToken);
const lexer = (arr) => arr.reduce((r,t) => ..., [])
const parse = (form) => {
  const syms = lexer(token(formula));
  let offset = 0;
  let buffer = traverse();
  return evaluate(buffer);

  function evaluate(array) {...}
}
```

옛날에 컴파일러 수업을 들었던 게 대충 생각나서 그래도 tokenizer로 식을 token
단위로 끊고, lexer로 symbol로 만들고(원래는 scan할 때 한 번에 하는 작업이었던
것으로 기억하지만), parse에서 Parse Tree를 만들어서 evaluator에서
실행(계산)하도록 만들려고 짜다가 구현을 할수록 구조는 복잡해지는데 비해 난이도는
올라가서 그냥 뒷부분은 한군데 섞어버렸다. 배운 걸 써먹기 위해서 이걸 다시
Haskell로 도전해보았다. 일단 문제를 단순화시켜서 괄호 없는 분자식을 구현해보자.
규칙은 다음과 같다.

1. 원소기호는 대분자와 소문자, 혹은 대문자로 이루어진다.
2. 개수는 원소기호 뒤에 오는 0자리 이상의 숫자로 없다면 1이 된다.
3. 원소기호와 개수를 합쳐서 동원소분자(homoatomic; 물론 제대로 된 명칭인지는
모름)라고 부른다.
4. 분자식은 동원소분자가 1개 이상 이어진다.

BNF라는 명칭만 기억나고 정확한 내용은 기억나지 않지만 비슷하게 옮겨보자면

- molecules  = homoatomic  | molecules
- homoatomic = atom amount | atom

이제 이걸 적용할 수 있는 파서를 만들어보자. 일단 파서의 적절한 타입을
만들어야 하는데, 함수형에서는 어떤 흐름을 기록하는데 자주 쓰이는
[State](https://en.wikipedia.org/wiki/Monad_(functional_programming)#State_monads)라는
패턴이 있다. 모양은 대충 `type State s a = s -> (a, s)`가 된다. 즉, 현재의 어떤
상태`s`에서 그 상태에서 원하는 결과 `a`와 다음 상태`s`를 동시에 가져오게 된다.
상태는 변이(tramsform)되지만 원하는 값을 얻으면서 변수의 불변성(immutability)도
만족해서, 메모리만 충분하다면 구조에 따라 상태 변이를 기록할 수 있어서 추적하기
쉽다는 장점이 있다. 다시 돌아와서, State처럼 파싱할 문자열을 받아서 어떤 연산을
규칙에 맞는 결과물 `T`를 가져온 뒤 남은 문자열과 함께 돌려주는 일종의 State같은
타입을 Parser라고 정의해서 사용할 것이다.

``` js JavaScript
// type Parser[T] = String -> (T, String)
```

먼저 앞에서 구현했던 것처럼 token을 가져오는 Parser를 만들어보자. Haskell의
경우에는 강한 타입에다 패턴 매칭을 지원하는 언어라서 원하는 타입이나 원하는
형태가 아니면 실패했다고 돌려줄 수 있지만, JavaScript는 둘 다 지원되지 않아서
내가 원하는 것이 맞는지 글자를 소모하지 않고 확인할 수 있는 기능과 글자를
소모해서 원하는 것인지 확인하는 기능 두 가지를 통해 tokenizer를 구현할 수 있다.

``` js JavaScript
///// Parser => Parser
const ahead   = p => (str) => p(str) ? ['', str] : [];
///// Parser => Parser
const satisfy = p => (str) => p(str) ? [str[0], str.substr(1)] : [];
```

실패`[]`의 경우는 같고, 성공할 경우에 `ahead`는 아무것도 매칭하지 않은 채
원래의 문자열`str`을 돌려주고, `satisfy`의 경우 매칭된 글자와 나머지 문자열을
돌려준다. 원래는 튜플(Tuple)로 구현하는 게 좋지만, JavaScript에 그런 게
있을 리가.

이제 다음 글자가 대문자라는 파서를 만들려면 다음과 같이 만들면 된다.

``` js JavaScript
const upper = satisfy(ch => 'A' <= ch[0] && ch[0] <= 'Z');

console.log( upper("H") ); // [ 'H', '' ]
```

하지만 타입 확인도 패턴 매칭도 존재하지 않아서 적절한 입력인지 확인할 수 있는
규칙과 그것을 합성할 수 있는 기능이 필요하다. 물론 합성 기능은 다음 글자가
소문자라는 파서를 만들었을 때 그 파서와 합성하는 데 사용할 수도 있다.

``` js JavaScript
// (Parser, Parser) -> Parser
const and = (pa, pb) => (str) => {
  const ra = pa(str);
  if(ra.length === 0) return [];
  const rb = pb(ra[1]);
  if(rb.length === 0) return [];
  return [ra[0] + rb[0], rb[1]];
};
```

이제 첫 번째 파서가 성공할 경우 `[match1, rest1]`를 받아서 `rest1`을 다음 파서에
넘겨서 `[match2, rest2]`를 가져온 뒤에 `[match1 + match2, rest2]`라는 결과를
만들어주는 파서를 생성할 수 있게 되었다. 물론 둘 중 하나라도 실패하면 실패`[]`가
리턴된다. 이걸 사용해서 소문자 확인과 결합해보자.

``` js JavaScript
const lower = satisfy(ch => 'a' <= ch[0] && ch[0] <= 'z');

console.log(and(upper, lower)("Mg")); // [ 'Mg', '' ]
```

그리고 앞서 만들어놓은 `ahead`와 더불어 원하는 조건인지 확인을 합성할 수도 있다.

``` js JavaScript
const chr   = ahead(str => str.length > 0);

const upper = and(chr, satisfy(ch => 'A' <= ch[0] && ch[0] <= 'Z'));
const lower = and(chr, satisfy(ch => 'a' <= ch[0] && ch[0] <= 'z'));
const digit = and(chr, satisfy(ch => '0' <= ch[0] && ch[0] <= '9'));
```

다음 글자가 존재하는지, 존재할 때 원하는 범위의 글자가 맞는지를 한 번에 확인할
수 있는 파서들이 만들어졌다. 이제 원소기호를 파싱할 때 대문자와 소문자가
연속으로 올 때도 있지만, 대문자만 존재할 수 있으니 `or`를 만들어보자.

``` js JavaScript
///// (Parser, Parser) -> Parser
const or = (pa, pb) => (str) => {
  const r = pa(str);
  if(r.length > 0) return r;
  else             return pb(str);
};
```

`and`처럼 파서 두 개를 받아서 새로운 파서를 만들어주는데, 차이는 첫 번째 파서가
실패하면 바로 실패했다는 결과를 넘겨주고 뒤의 규칙은 확인하지 않으며, 두 번째
파서가 실패할 경우에는 그 자체가 실패의 결괏값이므로 그냥 넘겨주면 된다. 이걸로
원소기호를 가져와 보자.

``` js JavaScript
const atom   = or(and(upper, lower), upper);

console.log(atom("Mg")); // [ 'Mg', '' ]
console.log(atom("H"));  // [ 'H', '' ]
```

이제 둘 다 만족하게 되었다. 그럼 다음은 숫자를 0개 이상 받을 때인데, 재귀적인
규칙이라 앞에서 구현한 것들보다 약간 복잡해진다.

``` js JavaScript
const amount = or(and(digit, amount), digit);
```

ES6에서 생긴 상수/변수인 `const`와 `let`은 호이스팅(hoisting)되지 않아서 이렇게
만들면 작동하지 않는다. 그래서 함수가 실행된 뒤에 `amount`를 읽을 수 있도록
소스가 약간 길어지지만 감수하면서 만들자면 다음과 같다.

``` js JavaScript
const amount = (str) => or(and(digit, amount), digit)(str);
```

하지만 0개 이상을 읽는 경우가 이번뿐이 아니므로 이걸 좀 다듬어보자.

``` js JavaScript
const many = (p) => (str) => or(and(p, many(p)), p)(str);
const amount = many(digit);
```

이제 `many`를 사용하면 나머지를 완성할 수 있다.

``` js JavaScript
const homo   = or(and(atom, amount), atom);
const mole   = many(homo);
```

원소기호와 개수가 나오는 경우 혹은 원소기호만 있는 경우가 0번 이상 반복되는 것을
분자식이라고 한다. 아예 받지 않거나 실패하는 경우에는 아무것도 출력하지 않도록
결과를 다듬어보자.


``` js JavaScript
const parse  = (p) => (str) => or(p, (str) => ['', str])(str)[0];
console.log(parse(mole)("H2O")); // H2O
```


이제 `satisfy(ch=>ch=='(')` 같은 식으로 열고 닫는 괄호를 3종류로 나누어서 규칙을
만들고 `and`와 `or`로 잘 조합하면 원래 문제의 답을 얻을 수 있을 것이다. 맨 위에
적어놓은 것보다 훨씬 깔끔한 구조로 함수들을 연결해서 답을 만들어냈다. 다만 현재
JavaScript 엔진들은 스코프를 생성하는 비용이 비싸서 함수 호출이 많아질수록 꽤
느려질 수 있다. 다만 불변성 구조가 유행하고 있고 엔진들도 최적화가 많이 진행되고
있으므로 trade-off를 통해 성능은 비슷하지만 구조는 깔끔하게 만들 수 있지 않을까
기대한다.

예전에 Rx, LINQ 등을 만든 에릭 마이어(Erik Meijer)가 edX에서 함수형 프로그래밍
입문을 강의한다길래 반가운 마음에 들어봤는데, 이상한 아저씨가 이상한 배경에
하이톤으로 함수형에 대해 역사부터 열심히 설명하길래 거기까지만 듣고 포기했던
적이 있다. 이번에 읽은 책이 Haskell 자체보다는 함수형 개념에 관해 설명한 책이라
Haskell로 문제를 풀어보면서 모르는 게 있어서 리뷰하려고 다시 들어봤더니 여전히
그 배경은 적응이 안 되지만 그래도 친절하고 좋은 강의였다(참고로 아래의 Contents
링크에 들어가면 화질별 강의와 슬라이드, 자막이 있다). 10월 15일에 다음 강의가
열리는데, 함수형이나 Haskell 입문에 관심 있는 분들께는 시각적인 어려움을
참작하고라도 매우 도움이 되는 강의니 추천한다.

ps, 구직중

---

Reference

- [Monadic Parser Combinators - Graham Hutton, Erik Meijer](https://www.cs.nott.ac.uk/~gmh/monparsing.pdf)

- Codewars
  + [Molecule to atoms](http://www.codewars.com/kata/molecule-to-atoms)

- Hackage: Official Haskell Package archive
  + [Text.ParserCombinators.ReadP](https://hackage.haskell.org/package/base-4.8.1.0/docs/Text-ParserCombinators-ReadP.html)

- Haskell/Wikibooks
  + [Haskell/ParseExps](https://en.wikibooks.org/wiki/Haskell/ParseExps)

- FP101x
  + [edX course 10/15~](https://www.edx.org/course/introduction-functional-programming-delftx-fp101x-0)
  + [Contents](https://github.com/fptudelft/FP101x-Content)
