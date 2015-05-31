title: 흥미로운 ECMAScript 제안들
date: 2015-05-31 16:58:22
tags: 
- ES6
- observable
---

RSS에 글이 뜸해질 때는 [Coven](http://www.coven.link/)을 보는데, [Hacker News](https://news.ycombinator.com/), [/r/programming
](http://www.reddit.com/r/programming)(Reddit의 프로그래밍 서브레딧- 게시판 개념) 등 사이트 4개의 추천 상위 글을 실시간으로 가져오는 곳이다.
다른 곳에도 공유하긴 했지만, 여기 올라온 글 중에서 흥미로운 ES의 제안(제안-초안-후보-마감의 단계 중 첫 번째)에 해당하는 것 2개를 소개하려고 한다.



## 1. ["use strong" mode in ES6](https://docs.google.com/document/d/1Qk0qC4s_XNCLemj42FqfsRLp49nDQMZ1y7fwf5YjaI4/preview)

ES5의 "use strict"를 포함하는 개선된(빡쎈) 버전이다. V8의 경우 예측할 수 있는 경우에는 최적화된 컴파일러를, 아닌 경우에는 일반 컴파일러를 실행한다고 
되어있는 [글](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers#some-v8-background)이 있었는데, 다른 구현체들의 
정확한 internal은 잘 모르겠지만 컨셉 자체는 다른 곳에도 적용가능해보인다. 다시 말해서, 프로그램 흐름과 객체 상태를 제약해서 유지보수를 편하게 하고
성능을 좋게 한다는 아이디어에 대한 구체적인 제약들이다. 대표적으로 `var`와 `delete`를 금지하는 문법상 제약이 있다. `var`를 금지하는 대신 `let`을
사용해서 lexical scope의 범위를 줄여서 컴파일러나 사람이 생각해야하는 양을 줄이겠다는 것으로 보인다. 그리고 `delete`를 금지하면 객체의 형태가 고정
(seal)되어 형태에 대한 정보를 재사용하고 예측가능해진다.
(참고, ES6의 `Object.freeze`와 `Object.seal`의 차이에 대한 [답변](http://stackoverflow.com/questions/21402108/difference-between-freeze-and-seal-in-javascript))


## 2. [ES2016 Observable](https://docs.google.com/file/d/1uEVcOgJIMsHjN1vypKKyfmDRg_bz5cKXpo0v4Nc0q8NfqKolBeSDHIj8z9GS8A4EiMpZ8QQ3l87Q_wF3/preview)

ES6의 Generator와 ES7의 async/await를 합치면 비슷한 구현은 가능하지만, async generator의 경우에 IO에는 적합하지만 Push(Event, WebSocket)
등에는 적합하지 않는다. 즉 async는 producer를 observable은 consumer를 만들 때 적합하다(하지만 이해를 잘못한 것인지 슬라이드에는 반대로 되어있다).
사실 이게 들어갈 확률은 매우 적어 보이지만, 요즘 듣고 있는 Coursera의 [reactive programming](https://class.coursera.org/reactive-002/)에서
Rx에 대한 내용이 나오다 보니 그쪽으로 관심이 생겨서 RxJS도 써보고 싶은데 마침 이 제안을 발견해서 기억에 남았다.

---

ps 1, 이것들 말고도 hn/reddit에 추천 상위로 올라온 게 더 있지만 재미가 없었는지 키워드도 기억나지 않는 게 많다. 

ps 2, 흥미로운 것들을 발견하면 실시간으로 트위터에 올리고 있다. [@devthewild](https://twitter.com/@devthewild)

ps 3, 구직중