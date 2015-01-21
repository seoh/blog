title: underscore.js로 편해지자
date: 2012-10-09 10:21
tags: underscore
---

> ## Translation of "[Getting Cozy With Underscore.js](http://code.tutsplus.com/tutorials/getting-cozy-with-underscore-js--net-24581)" into Korean, under the same license as the original.

들어가기 전에, [functional javascript](http://almostobsolete.net/talks/functionaljs/) 자바스크립트의 함수형 프로그래밍에 대한 간략한 소개가 있는 슬라이드. 함수형 프로그래밍에 개념과 underscore의 컨셉을 이해하는데 도움이 된다.

---

# Underscore.js와 만나다

그래서 Underscore가 정확히 뭐하는건데?

> Underscore는 Prototype.js(혹은 Ruby)처럼 기본 JavaScript 객체들을 확장하지 않고 함수형 프로그래밍을 지원할 수 있는 유용한 JavaScript Library이다.

Python이나 Ruby로 작업할 때 더 좋은 점은 훨씬 쉽게 쓸 수 있는 `map`같은 멋진 기본함수들이 있다는 것이다. 슬프게도 JavaScript의 현재 버전에서는 그런 native로 제공되는 것들이 꽤나 부실하다. 위에서 읽은 것처럼, Underscore.js는 단지 4kb라는 터무니없는 용량의 좀 멋진 JavaScript Library다.

---

## Underscore in Action

"라이브러리 이야기는 충분히 많이 들었다", 라는 말할지도 모른다. 맞다, 하지만 일단 한번 보자. 임의의 시험점수 배열에서 90점 넘는 리스트가 필요하면 보통은 이렇게 짤 것이다.

```js
var scores = [84, 99, 91, 65, 87, 55, 72, 68, 95, 42],
topScorers = [], scoreLimit = 90;

for (i=0; i<=scores.length; i++)
{
   if (scores[i]>scoreLimit)
   {
      topScorers.push(scores[i]);
   }
}

console.log(topScorers);
```

꽤 깔끔하고 심지어 최적화도 있는데, 내가 하려는 것보다는 좀 길다. underscore로 하면 이렇게 된다.

```js
var scores = [84, 99, 91, 65, 87, 55, 72, 68, 95, 42],
topScorers = [], scoreLimit = 90;

topScorers = _.select(scores, function(score){ return score > scoreLimit;});

console.log(topScorers);
```

넌 어떤지 모르겠지만 난 좀 너드같다. 저기있는 코드 굉장히 간결하고 읽기 좋다.


## 괜찮긴한데 진짜 필요해?

모든건 뭘 하려고 하는지에 달렸다. 단지 DOM 다루는데 사용을 제한한다면 jQuery로 원하는건 대부분 할 수 있다. 반대로, DOM이랑 관련없거나 좀 복잡한 MVC나 front-end 코드들이라면 underscore는 확실히 괜찮다. ECMA 스펙에 맞춰서 천천히 라이브러리들의 기능이 업데이트되는 동안에, 모든 브라우져에서 돌아가는건 가능하지도 않고 여러 브라우저들에서 돌아가게 하는 것도 또다른 악몽이다. underscore는 어디서든 작동하도록 좋은 추상화를 제공한다. 그리고 퍼포먼스를 중시하는 사람이거나 그래야하는 사람이면 underscore는 가능한 native 뒤에 구현된거라 가능한 최적화된 성능을 보장한다.

## 시작해보자

> [여기](http://underscorejs.org/docs/underscore.html)서 소스를 가져다 넣기만 하면 페이지는 잘 돌아간다.

적용시키는데 뭔가 큰 작업을 기대했으면 꽤나 실망할꺼다. 그냥 [여기](http://underscorejs.org/docs/underscore.html)에서 소스를 가져다 넣기만 하면 페이지는 잘 돌아간다. underscore는 global 영역에서 단일 객체로 만들어지고 모든 기능을 한다. 이 객체는 underscore 문자, _로 불린다. 흥미롭게도 jQuery가 dollar 문자($)로 돌아가는 것과 매우 비슷해 보인다. 또 jQuery처럼 충돌을 피하려면 다른 문자로 쓸 수도 있다.

## 함수형? 아니면 객체지향?

라이브러리의 공식적인 광고에는 함수형 프로그래밍 지원이라고 써있는데, 실제로 쓰는 다른 방법도 있다. 저 위에서 썼던 예제를 가져와보면

```js
var scores = [84, 99, 91, 65, 87, 55, 72, 68, 95, 42], topScorers = [], scoreLimit = 90;

topScorers = _.select(scores, function(score){ return score > scoreLimit;});

console.log(topScorers);
```

위의 방법은 함수형이나 절차지향적인 접근이다. 또 더 직관적이며 아마 더 확실하게 객체지향적으로 사용할 수도 있다.

```js
var scores = [84, 99, 91, 65, 87, 55, 72, 68, 95, 42], topScorers = [], scoreLimit = 90;

topScorers = _(scores).select(function(score){ return score > scoreLimit;});

console.log(topScorers);
```

실제로 '정답'이라는건 없지만, jQuery식으로 method 뒤에 chainging을 할 수 있다는 것을 알아두라.


## 기능을 확인해보자

underscore는 기능 자체의 숫자보다 많은 60개보다 조금 넘는 함수를 지원한다. 돌아가는 함수들을 그룹으로 다음과 같이 분류할 수 있다.

- Collections
- Arrays
- Objects
- Functions
- Utilities

각각 뭘 하는지를 보고, 가능하면 섹션에서 한두개쯤 적용해보겠다.


## Collections

connection은 배열이나 객체가 될 수 있고, 의미상으로 말하자면 자바스크립트에서의 (객체와 배열이)혼합된 배열이다. underscore는 collections에서 돌아가는 많은 method들을 제공한다. 위에서 이미 `select`를 봤다. 여기 더 쓸만한게 하나 있다.

### Pluck

key와 value 쌍으로 이루어진 깔끔하고 짧은 배열이 있는데 여기에서 특정 속성을 추출하려고 한다. underscore로는 아주 쉽다.

```js
var Tuts = [{name : 'NetTuts', niche : 'Web Development'}, {name : 'WPTuts', niche : 'WordPress'}, {name : 'PSDTuts', niche : 'PhotoShop'}, {name : 'AeTuts', niche : 'After Effects'}];
var niches = _.pluck(Tuts, 'niche');

console.log(niches);

// ["Web Development", "WordPress", "PhotoShop", "After Effects"]
```

`pluck`을 쓰면 객체나 배열을 훑는 것만큼 속성을 뽑아내는 것은 간단하다. 각 사이트들이 분야(niche)에 속하는지 추출해봤다.

### Map

map은 collection으로부터 함수를 통해 각 요소들을 바뀐 배열을 만든다. 앞에서 있던 예제에서 조금 더해봤다.

```js
var Tuts = [{name : 'NetTuts', niche : 'Web Development'}, {name : 'WPTuts', niche : 'WordPress'}, {name : 'PSDTuts', niche : 'PhotoShop'}, {name : 'AeTuts', niche : 'After Effects'}];

var names = _(Tuts).pluck('name').map(function (value){return value + '+'});

console.log(names);

// ["NetTuts+", "WPTuts+", "PSDTuts+", "AeTuts+"]
```

이름 끝에 +를 붙이도록 해서, 추출된 배열에는 +가 추가되어있다. 간단한 연쇄(method chaining; 여기에서는 pluck+map)를 하는데 제한은 없다. 지나가는 값들을 원하는데로 다 바꿀 수 있다.

### All

`all`은 collection 속의 모든 값들이 어떤 기준에 만족하는지 확인하는데 매우 유용하다. 예를 들어, 학생이 모든 과목에서 통과했는지 확인해보자.

```js
var Scores = [95, 82, 98, 78, 65];
var hasPassed = _(Scores).all(function (value){return value>50; });
console.log(hasPassed);
// true
```


## Arrays

underscore에만 있는 배열을 다루는 함수들이 굉장히 평이 좋은데, 다른 언어에 비해 JavaScript는 배열을 다루는 함수가 거의 없어서다.

### Uniq

이 method는 기본적으로 배열을 읽어서 중복값을 제거하고 유일한 값들만 돌려준다.

```js
var uniqTest = _.uniq([1,5,4,4,5,2,1,1,3,2,2,3,4,1]);

console.log(uniqTest);

// [1, 5, 4, 2, 3]
```

거대한 데이터들을 추려서 중복값을 제거하는데 무척 편하다. 원래 배열의 순서를 유지하기 때문에 중복값 중 첫번째 값이 나온다는걸 명심하자.

### Range

어떤 '범위'나 숫자들의 리스트를 만들 때 무척 편하다. 빠르게 예제를 보자

```js
var tens = _.range(0, 100, 10);

console.log(tens);

// [0, 10, 20, 30, 40, 50, 60, 70, 80, 90]
```

파라미터는 순서대로 시작하는 값, 끝나는 값, 그리고 차이값(step)다. 감소하는 범위를 만들고 싶으면 음수의 차이값을 쓰면 된다.


### Intersection

이 method는 각 배열들을 두 배열씩 비교해서 같은 모든 배열에 있는 값들의 배열을 돌려준다. 집합론에서의 교집합같은 것이다. 위에서 나왔던 예제를 확장해서 이게 어떻게 돌아가는지 보자.

```js
var tens = _.range(0, 100, 10), eights = _.range(0, 100, 8), fives = _.range(0, 100, 5);

var common = _.intersection(tens, eights, fives );

console.log(common);

// [0, 40, 80]
```

쉽지 않나? 비교할 배열들만 써놓으면 underscroe가 나머지는 해준다.

## Objects

*is* 메소드들은 뭔가를 확인하는데 쓰일꺼라 예상할 수 있고(예를 들어 isEmpty, isEqual 등), underscore는 객체를 복사하거나 확장하거나 기타 등등을 다루는 여러 method들을 제공한다.

### Keys and Values

커다락 객체에서 key들만 필요하거나 value들만 필요하면? 쉽게 가져올 수 있다.

```js
var Tuts = { NetTuts : 'Web Development',  WPTuts : 'WordPress',  PSDTuts : 'PhotoShop', AeTuts : 'After Effects'};
var keys = _.keys(Tuts), values = _.values(Tuts);

console.log(keys + values);
// NetTuts,WPTuts,PSDTuts,AeTutsWeb Development,WordPress,PhotoShop,After Effects
```

### Defaults

기본으로 사용해야하는 값들과 함께 객체를 만드는데 꽤 유용하다.

```js
var tuts = { NetTuts : 'Web Development'};
var defaults = { NetTuts : 'Web Development', niche: 'Education'};
_.defaults(tuts, defaults);

console.log(tuts);

// Object { NetTuts="Web Development", niche="Education"}
```

## Functions

좀 이상하게 들리지만, underscore는 함수에서 돌아가는 함수들을 갖고 있다. 함수들의 대부분은 설명하기 복잡한 것들이어서 제일 간단한 것을 가져왔다.

### Bind

`this`는 JavaScript에서 설명하기 힘든 부분이고 많은 개발자들을 멘붕하게 만든다. 이 method는 그나마 건들기 쉽게 해준다.

```js
var o = { greeting: "Howdy" },
    f = function(name) { return this.greeting +" "+ name; };

var greet = _.bind(f, o);

greet("Jess")
```

헷갈려서 계속 쳐다보게 만든다. bind 함수는 기본적으로 함수가 어디에서 언제 호출이되건 `this`값을 유지하게 해준다. `this`를 가져다가 event를 다룰 때 특별히 유용하다.


## Utilities

그리고 쓰기에 더 괜찮은게, underscore는 utility 함수들을 대량으로 지원한다. 시간이 없으니 크고 아름다운 것 하나만 보자.

### Templating

이미 template을 만드는 많은 solution들이 있지만 underscore는 강력하면서 구현이 적다는데 장점이 있다. 대충 예제를 하나 만들어보면

```js
var data =   {site: 'NetTuts'}, template =   'Welcome! You are at <%= site %>';

var parsedTemplate = _.template(template,  data );

console.log(parsedTemplate);

// Welcome! You are at NetTuts
```

우선, template를 통해 template으로 옮겨서 데이터를 만들 수 있다. 기본적으로, underscore는 모두 custom할 수 있는 ERB(embeded Ruby) 스타일 문자를 사용한다. 그 자리에, template과 data를 가지고 `template`을 간단하게 호출하면 된다. 필요하면 나중에 업데이트하기 위해 분리된 문자열에 결과를 저장한다.

이건 underscore의 template 중에서 극단적으로 간단한 예제라는걸 명심해라. `<% %>` 문자를 이용한 template 안쪽에서 어떤 JavaScript 코드를 사용할 수 있다. JSON같은 복잡한 객체를 훑을 필요가 있을 때에는 underscore의 우수한 collection 함수들과 함께 template을 빠르게 만들 수도 있다.

## 여전히 왜 써야하는지 모르겠다면

> jQuery와 underscore를 함께

jQuery와 underscore는 상호보완적인 것이라 함께 쓰면 더 좋다. 자, jQuery는 아주 괜찮은 케이스들도 있다. DOM을 다룬다거나 애니메이션같은 것들이 말이다. 하지만 더 고수준이나 저수준을 다루지는 않는다. 고수준 이슈를 다루는 Backbone이나 Knockout같은 프레임웍이라면 필요한 부분에 전부 끼워넣을 수 있다.

좀 더 넓게 보자면, jQuery는 DOM을 다루는 기능 때문에 브라우저 밖에서는 거의 쓸 일이 없다. 다른 면으로 underscore는 브라우져에서도 사용할 수 있고 server단에서도 별 이슈없이 사용할 수 있다. 사실, underscore는 node module들이 제일 많이 의존하고 있다. 지금 말한건 그렇다. underscore의 활용에 대해서는 겉만 살짝 긁은 정도다.
