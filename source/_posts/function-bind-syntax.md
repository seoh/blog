title: ES7의 함수 bind 문법
date: 2015-06-11 02:02:01
tags:
- ES7
- babel
---



어제 @wesbos가 `.bind()`를 사용한 `querySelector` 단축에 대한 js 소스를 캡쳐한 [트윗](https://twitter.com/wesbos/status/608341616173182977)을 올렸다.

``` js
// 선택된 첫번째 엘리먼트 - $('input[name="food"]')
var $ = document.querySelector.bind(document);
// 선택된 엘리먼트들의 배열 - $('img.dog')
var $$ = document.querySelectorAll.bind(document)
```

여기에 @paul_irish가 비슷한 컨셉으로 이벤트 등록까지 할 수 있는 [bling.js](https://gist.github.com/paulirish/12fb951a8b893a454b32)를 [리플](https://twitter.com/paul_irish/status/608433593061376003)로 남겼다.

``` js
Node.prototype.on = window.on = function(name, fn) {
    this.addEventListener(name, fn);
}
// 이하 생략
```

이런 @thejameskyle가 흥미로운 [리플](https://twitter.com/thejameskyle/status/608438113703182338)로 [이런 소스](http://t.co/dQFFJHYZtZ)를 남겼다.

``` js
let $ = ::document.querySelector;
let find = Element.prototype.querySelectorAll;
let each = Array.prototype.forEach;
let on = Node.prototype.addEventListener;
// 이하 생략
```

생소한 문법이 등장했다. `::`는 어떤 의미일까?

---

예전에 봤던 reactjs로 ES6를 접한 사람들이 하는 오해에 대한 글, [ES6 Gotchas](https://reactjsnews.com/es6-gotchas/)가 생각났다.

``` js
<div onClick={this.handleClick} />
```

이게 사실은

``` js
import React from 'react'

export default class ComponentName extends React.Component {
  constructor(){
    super()
    this.handleClick = this.handleClick.bind(this)
  }
  ...
```

이런 식으로 `this.handleClick`을 그대로 넘기는게 아니라 자동으로 bind해서 넘겨주는걸 react에서 해주기 때문에 사람들이 오해하기 쉽다는 뜻이다.
babel에서는 (소위 ES7이라 부르는)실험 기능을 활성화할 수도 있는데, 그 중 데코레이터에 [autobind decorator](https://github.com/andreypopp/autobind-decorator)를 붙일 수도 있다.
`@autobind`를 메소드 혹은 함수 위에 붙이면 react처럼 bind해주는 기능을 한다. 그런데 이게 마음에 안 찼는지 [함수 Bind 문법](https://github.com/zenparsing/es-function-bind)이라는 제안이
나왔고, babel에도 [추가](http://babeljs.io/blog/2015/05/14/function-bind/)되었으며 자세한 설명된 [포스팅](http://blog.jeremyfairbank.com/javascript/javascript-es7-function-bind-syntax/)도 올라왔다. 위에 'ES6 Gotchas' 댓글에서 예제 소스를 빌려오자면,

1. `::a.b`는 `a::a.b`와 같다.
2. `a::b.c`는 `b.c.bind(a)`와 같다.
3. 그러므로 `::a.b`는 `a.b.bind(a)`와 같다.

이제 다시 처음 소스의 `::document.querySelector`를 풀어보자면 `document.querySelector.bind(document)`라는 것을 알 수 있다.

---

ES6/ES7(이제는 ES2015 or after라고 부르지만)에 대한 이야기를 자주 하는 이유는 재미있고 내가 관심 있는 분야라서도 그렇지만,
이런 기술들은 나 혼자 편하다고 해서 쓸 수 있는 것이 아니다. 같이 일하는 사람들이 필요성에 대해 공감하고 익숙해야 쓸 수 있다.
그래서 보다 많은 사람들이 관심을 가지고 익숙해졌으면하는 바람이 있다. (물론 나는 현재 무직이라 같이 일하는 사람이 없지만 나중을 위해)

JavaScript라는 언어의 문법 자체는 심플한 편이지만, 거지같은 실행환경 때문에 `this`가 뭐냐 언제 결정되느냐는 질문을 하도 많이 받아서
["var that = this" 같은 자바스크립트 스코프에 대한 이해](http://devthewild.tumblr.com/post/73112749480/var-that-this)라는
StackOverflow의 답변을 번역해 글을 쓴 적도 있다. 어차피 웹이라는 거대한 플랫폼에서 자유로운 개발자는 많지 않고, 발을 디딘 사람들 중 대부분은 프론트를 한 번쯤 만지게 된다.
피할 수 없다고 즐기는 건 변태지만, 최대한 생산성을 높여서 괴로움을 줄이길 바란다.