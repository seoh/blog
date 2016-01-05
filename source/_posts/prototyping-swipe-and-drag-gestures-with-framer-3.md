title: Framer에서 swipe/drag 제스쳐 프로토타이핑하기
date: 2014-09-10
tags:
- framerjs
- prototyping
---

> ### Translation of "[Prototyping swipe and drag gestures with Framer 3](https://medium.com/@gem_ray/prototyping-swipe-and-drag-gestures-with-framer-3-2e405d50b600)" into Korean, under the same license as the original.

우리는 [Potluck](https://www.potluck.it/)에서 iOS앱에서 인터렉션을 테스트해보고 프로토타이핑을 하기 위해 Framer.js를 사용하기 시작했다. Framer는 모바일과 데스크탑 앱에서의 인터렉션 프로토타이핑과 에니메이션을 위한 자바스크립트 프레임워크다. 쿼츠 콤포저(Quartz Composer)보다 러닝커브가 훨씬 낮고 아이폰과 데스크탑 양쪽에서 쉽게 실행할 수 있는 훌륭한 대안이다. 키노트에서 시작해서 실제같은 인터렉티브 프로토타입을 단계별로 만들 수 있다.

자바스크립트 기반이기 때문에 특히 드래그나 스와이프같은 복잡한 제스쳐를 만들 때 좋다. 스와이프 거리에 따라 엘리먼트를 움직이거나 사라지게할 때 혹은 특정거리 이상 스와이프할 때 액션이 발생하도록 할 수 있다.

신생 프로젝트라 예제나 문서들이 너무 없다. 그래서 배웠던 것들을 공유하려고 생각했다.

---

시작하기 전에 타이핑하기 귀찮은 사람을 위해 [CodePen](http://codepen.io/seoh/full/AqycD에 완성본을 올려놨다.

# Activate on release
다른 액션을 가진 테이블 셀들로 간단한 예제를 시작해보자.

일단 보통 아이폰 스크린을 표시하기 위한 컨테이너 뷰를 만들어보자. 시스템 상태바를 가진 웹앱을 프로토타이핑하기 위해 높이는 598px로 정했다. (역주, 원문에서는 1096px였지만 웹에서 확인하기 좋게 절반으로 줄였다.)

<p data-height="630" data-theme-id="0" data-slug-hash="teAkE" data-default-tab="js" data-user="seoh" class='codepen'></p>

셀에서 사용자에게 보이는 부분을 정한 컨테이너를 만들고, 화면 밖에서 슬라이드하면 셀이 나타나도록 할 것이다.

<p data-height="630" data-theme-id="0" data-slug-hash="hrvAG" data-default-tab="js" data-user="seoh" class='codepen'></p>

이렇게 보일 것이다.

![]/images/prototyping-swipe-and-drag-gestures-with-framer-3/1.png)

이제 셀에 드래그를 추가해보자.

<p data-height="630" data-theme-id="0" data-slug-hash="lqmjo" data-default-tab="js" data-user="seoh" class='codepen'></p>

원하는 기능은 아니지만 어디로든 드래그할 수 있다.

![]/images/prototyping-swipe-and-drag-gestures-with-framer-3/2.gif)

---

더 구체적인 제스쳐를 구현하기 위해 드래그 이벤트 핸들러를 사용할 수도 있다. 뭔가 드래그할 때 세가지 이벤트가 발생한다. `Events.DragStart`는 드래그를 시작할 때 한번, `Events.DragEnd`는 드래그를 놓았을 때 한번 발생한다. `Events.DragMove`는 마우스나 손가락을 움직일 때 계속 발생한다.

draggable은 "speed" 속성을 갖는데 마우스 움직임에 따라 어느 정도의 속도로 움직일지 결정한다. 속도를 `0`으로 설정하면 물체가 해당하는 방향(축)으로 이동하지 않게 된다.

<p data-height="630" data-theme-id="0" data-slug-hash="DKtdp" data-default-tab="js" data-user="seoh" class='codepen'></p>

드래그가 끝났을 때 무엇을 할지 정할 수도 있는데, 여기에서는 원래 물체를 자리로 돌아가게 해보자.

<p data-height="630" data-theme-id="0" data-slug-hash="GHwLo" data-default-tab="js" data-user="seoh" class='codepen'></p>

![]/images/prototyping-swipe-and-drag-gestures-with-framer-3/3.gif)

---

이제 화면 밖의 엘리먼트가 드래그할 때 나타나도록 해보자. 그 엘리먼트는 셀 컨테이너의 차일드 뷰가 될 것이고(그래서 셀과 함께 움직일 것이고) 그냥 화면 밖에 놓도록 하겠다.

<p data-height="630" data-theme-id="0" data-slug-hash="liKwL" data-default-tab="js" data-user="seoh" class='codepen'></p>

이 액션 바를 추가한 다음에 셀을 왼쪽으로 끌면 화면 오른쪽 끝에서 나타나는걸 볼 수 있다.

![]/images/prototyping-swipe-and-drag-gestures-with-framer-3/4.gif)

좀 재미있는걸 넣어보자. 드래그 핸들러는 액션 바를 조금이라도 움직일 때마다 실행되는데, 얼마나 셀을 움직이는지에 따라 색깔을 바꾸게 만들 수 있다.

(얼마나 셀을 움직였는지에 대한)범위를 다른 (바의 색상)범위로 바꾸는 것은 `Utils.mapRange` 함수를 통해 쉽게 할 수 있다.

(역주: 원문은 원래 framerjs 2를 기준으로 작성되어 [Proccessing 프레임워크](http://processing.org/)의 `map_range`라는 함수를 사용했는데 중요한 정보는 아니다.)

이 함수에서는 `[low1, high1]` 범위에 있는 value를 `[low2, high2]` 범위에서의 같은 위치(비율)로 변환해준다. 이제 셀의 *x축 좌표*를 액션 바의 *투명도*로 바꿔보자.

<p data-height="630" data-theme-id="0" data-slug-hash="DHBev" data-default-tab="js" data-user="seoh" class='codepen'></p>

셀을 드래그할 때 액션 바의 색은 다음과 같이 변한다.

![]/images/prototyping-swipe-and-drag-gestures-with-framer-3/5.gif)

충분히 액션 바를 드래그했을 때는 그대로 열려있도록 만들고 싶다. 이걸 하려면, DragEnd 핸들러에서 얼마나 드래그되었는지 확인하고 특정 너비를 넘었다면 계속 열려있도록 해보자.

<p data-height="630" data-theme-id="0" data-slug-hash="qCdrl" data-default-tab="js" data-user="seoh" class='codepen'></p>

![]/images/prototyping-swipe-and-drag-gestures-with-framer-3/6.gif)


# Activate on threshold

탭을 밀었을 때 계속 열려있도록 해봤다. 탭까지 가지 않고 액션을 할 수는 없을까? iOS 6의 당겨서 새로고침이 좋은 예다. 충분히 당기면 탭까지 가지 않아도 새로고침이 일어난다. (역주, 여기에서는 특정 길이만큼 드래그 후 이벤트 발생으로 애니메이션을 실행하는 것까지만 예제로 한다.)

위아래로 움직이는 두번째 셀을 만들어보자.

<p data-height="630" data-theme-id="0" data-slug-hash="udyBe" data-default-tab="js" data-user="seoh" class='codepen'></p>

셀을 100px 아래로 내리면 쪼그라들게 해보자. 일단 이렇게 시도해봤다.

<p data-height="630" data-theme-id="0" data-slug-hash="ekgDo" data-default-tab="js" data-user="seoh" class='codepen'></p>

![]/images/prototyping-swipe-and-drag-gestures-with-framer-3/7.gif)

애니메이션이 좀 산만하다. 드래그할 때마다 계속 DragMove 이벤트가 발생하고 계속 애니메이션을 시작하려고 한다. 그래서 조건이 만족될 때 딱 한번만 발생하도록 해보자.

<p data-height="630" data-theme-id="0" data-slug-hash="trboC" data-default-tab="js" data-user="seoh" class='codepen'></p>

![]/images/prototyping-swipe-and-drag-gestures-with-framer-3/8.gif)

(역주, 원문에서는 Drag와 Animate 기능이 충돌해서 제대로 Animate가 작동하지 않아 Drag하는 레이어와 Animate되는 레이어를 따로 뒀는데 버그가 수정되었는지 잘 작동해서 그 이하 부분은 생략했다.)

---

# 아이폰에서 테스트

Framer Studio 1.7에서 Mirror라는 굉장히 편한 기능이 추가되었다. Studio에서 작업한 내용을 저장하고 Mirror 버튼을 누르면 작업중인 컴퓨터를 자동으로 서버로 사용해서 외부에서 접근할 수 있는 주소를 생성해준다. (역주, 원문에서는 Dropbox Public folder에 대한 내용이었지만 Mirror 기능이 더 유용하다고 생각해서 변경했다.)

<script async src="http://codepen.io/assets/embed/ei.js"></script>