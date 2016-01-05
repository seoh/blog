title: Elm Reousrces
date: 2015-08-10 18:34:02
tags: Elm
---

얼마전에 Haskell 책을 읽었더니, 예전에 관심은 있었지만 어디서부터 접근해야할지
몰라서 미뤄뒀던 [Elm][elm]이 생각났다. 어떤 것이라고 정리할 정도로 아직 잘 알지
못해서 또 미뤄두고 있었는데, 혹시나 관심을 가지고 있는 분이 있다면 함께
공부할 수 있었으면 좋겠다는 생각에 어떤 점에서 관심을 가지게 되었고 어떤 것을
봐왔는지 정리해두려고 한다.

time travel debugger
---

요새 react-hot-loader를 통해 hot swap(코드 수정 후 현재 상태까지 다시 컨트롤하지
않고 그 상태로 바로 반영하는 것)이 가능하다고 사람들이 환호했는데, 그 이전에
Clojurescript의 Leiningen plugin으로 [lein-figwheel][figwheel]이 있었다.

<div class="center">
  <iframe width="560" height="315" frameborder="0" allowfullscreen
          src="https://www.youtube.com/embed/j-kj2qwJa_E"></iframe>
</div>

하지만 여기에서 조금 더 발전한 [time travel debugger][debug]가 이미 있었다.
자세한 내용은 당시 [Hacker News][hn]의 코멘트를 참고.

<div class="center">
  <iframe width="560" height="315" frameborder="0" allowfullscreen
          src="https://www.youtube.com/embed/zybahE0aQqA"></iframe>
</div>

<div class="center">
  <iframe width="560" height="315" frameborder="0" allowfullscreen
          src="https://www.youtube.com/embed/vS3yzUo7l8Y"></iframe>
</div>

이게 가능한 것은 Elm의 runtime에 [Signal][signal]이라는 흐름 위에서 돌아가도록
설계되어있어서 그런 것이라 추정한다. (아직 뜯어보지 않았다.)


blazing fast
---

vDOM/react의 구현체 속도 벤치마크가 유행했던 적이 있는데 거기에서 뜬금없이
등장한 적이 있다. 홈페이지에도 [자랑][fast]이 올라와있는데 벤치마크 결과는
다음과 같다. 참고로 [Om][om]은 Clojurescript의 React 구현체다.

![]/images/elm-resources/1.png)

tutorial
---

공식 홈페이지에도 문서화나 예제가 잘 되어있긴 하지만 단계별로 따라갈만한 과정은
아직 없다. 그래서 검색하다가 유튜브의 [Elm Tutorial][tutorial]을 발견했는데,
문제는 이게 0.12 기준이라 현재(0.15.1)와 맞지 않다. 그래도 유일하게 존재하는
튜토리얼이라 공식 문서를 검색해가며 따라해봤고, 단계별로 소스를
[정리](repo)해봤지만 실시간으로 저장하며 따라한 것이라 따라가기 벅찰 때는
갑자기 단계가 휙 뛸 수도 있다. (그러므로 언제든 더 좋은 정리를 PR로!)

이 튜토리얼을 끝내고 얼마 뒤 유료로 [Elm: Building Reactive Web Apps][paid]라는
유료 강의가 올라왔는데, 들어보지않아서 어떤 강의인지는 잘 모르겠다.

other drugs
---

- [Functional Programming][uchicago]
  + 시카고대에서 올해 초 겨울학기 강의를 Elm으로 진행했다. 조만간 읽을 예정.
  + 0.14 기준이라 [Mailbox와 Task][0.15] 대신 Channel이 있어서 지금 그대로
    따라하기는 어렵다.
- [Learning FP the hard way: Experiences on the Elm language][gist]
  + Gist에 한페이지짜리 함수형 프로그래밍 소개글이 있지만 이것만으로 이해하기는
    어려워보인다.
- [Shipping a Production Web App in Elm][dreamwriter]
  + Dreamwriter라는 in-browser editor가 원래 React/Flux + CoffeeScript로
    만들어졌는데, 속도와 관리 때문에 Elm으로 넘어간 이야기.
- [Using Mailboxes in Elm][mailbox]
  + Actor Model처럼 Mailbox라는 개념을 통해서 Message를 보내는데 문서가 부족해서
    누가 대신 쓴 글
- [Similar libraries which were inspired by RxJS: Elm][rxbook]
  + rx-book의 한 챕터에 링크를 정리해놨는데 어떤 Elm 커뮤니티보다 잘해놨다.
- [Seven More Languages in Seven Weeks][seven2]
  + "You’ll learn seven exciting languages: Lua, Factor, Elixir, Elm, Julia, MiniKanren, and Idris."
  + 유일하게 Elm을 언급한 책인데 전작인 [세븐 랭귀지][seven]처럼 번역될 것 같아서 일단 기다리는 중.

---

간단하게 링크만 올릴 예정이었는데 쓰다보니 아직 제대로 이해하지 못한 불필요한
설명이 길어졌다. 하지만 아까워서 삭제하지 않고 그냥 발행.


[elm]: http://elm-lang.org/
[figwheel]: https://github.com/bhauman/lein-figwheel
[debug]: http://debug.elm-lang.org/
[hn]: https://news.ycombinator.com/item?id=7593032
[fast]: http://elm-lang.org/blog/blazing-fast-html
[om]: https://github.com/omcljs/om
[signal]: http://elm-lang.org/guide/reactivity
[tutorial]: https://www.youtube.com/playlist?list=PLtdCJGSpculbDT_p4ED9oLTJQrzoM1QEL
[repo]: https://github.com/seoh/bluepill
[paid]: https://pragmaticstudio.com/elm
[uchicago]: https://www.classes.cs.uchicago.edu/archive/2015/winter/22300-1/
[gist]: https://gist.github.com/ohanhi/0d3d83cf3f0d7bbea9db
[dreamwriter]: https://presentate.com/rtfeldman/talks/shipping-a-production-web-app-in-elm
[mailbox]: https://gist.github.com/mgold/461dbf37d4d34767e5da
[0.15]: http://elm-lang.org/blog/announce/0.15#introducing-mailboxes
[rxbook]: http://xgrommx.github.io/rx-book/content/similar_libraries/index.html#elm
[seven2]: https://pragprog.com/book/7lang/seven-more-languages-in-seven-weeks
[seven]: http://www.hanbit.co.kr/book/look.html?isbn=978-89-6848-185-7

<style type="text/css">
.center { text-align: center; }
</style>
