title: V8 Optimization killers
date: 2014-06-28
tags:
- V8
- optimize
- anti-pattern
---

> ### Translation of "[Optimization killers](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)" into Korean, under the same license as the original.

### 도입

이 문서는 예상보다 훨씬 성능이 떨어지는 코딩을 피하기 위한 조언이 포함되어 있다. 특히 V8(nodejs, Opera, Chromium 등)에서 최적화를 방해하는 패턴에 대한 것이다.

### V8 배경지식 약간
V8에서는 인터프리터가 없고 2가지 컴파일러가 있다, 일반 컴파일러(generic)와 최적화 컴파일러(optimizing). 
즉, JavaScript 코드를 항상 컴파일하고 네이티브로 직접 실행한다는 뜻이다. 이는 빠르다는 뜻이다, 정말? 아니다. 네이티브로 컴파일된 코드라고 해서 성능적으로 큰 의미가 있는 것은 아니다. 단지 인터프리터 과부하를 제거한 것이라 최적화되지 않은 코드는 여전히 느리게 돌아간다.

예를 들어, 일반 컴파일러로 `a + b`는 다음과 같다.

```asm
mov eax, a  
mov ebx, b  
call RuntimeAdd  
```

위에서 보면 단지 런타임 함수를 호출한다. `a`와 `b`가 항상 정수라면 다음과 같이 된다.

```asm
mov eax, a  
mov ebx, b  
add eax, ebx  
```

런타임에서 복잡한 의미가 추가된 스크립트보다 훨씬 빠르게 작동할 것이다.

전자는 일 컴파일러에서 나오는 코드고, 후자는 최적화 컴파일러에서 나오는 코드다. 최적화 컴파일러로 컴파일된 코드는 대충 일반 컴파일러로 생성한 것보다 100배쯤 빠르다. 여기서 잠깐, 그냥 JavaScript로 코딩한다고 최적화된다는 것은 아니다. 많은 패턴들이 있는데, 최적화 컴파일러가 건들지 못하는 관용적인 것들이 있다.

중요한 점은, 이렇게 최적화 제외 코드는 그 함수를 포함하는 전체에 영향을 준다는 것이다. 코드는 한번에 한 함수만 최적화가 되고, 다른 코드가 무엇을 하든 전혀 모르고 있다(현재 최적화하고 있는 함수 내에 인라인된 코드가 아니라면).

이 가이드는 "비최적화"의 함수를 포함해 대부분의 패턴을 다룬다. 최적화 컴파일러가 더 많은 패턴을 인식할 수 있도록 업데이트 될 때 이런 우회 방법들은 불필요해질 것이다.

(본문은 요약)

### 주제
1. [툴링](#1-_툴링)
2. [미지원 문법](#2-_미지원_문법)
3. [`arguments` 관리](#3-_arguments_관리)
4. switch-case
5. for-in


### 1. 툴링
최적화에 영향을 주는지 알아보기 위해 V8의 내부에서 사용하는 플래그를 사용할 수 있다.

HackerNews의 댓글을 보면 플래그 등에 대한 [추가 설명](https://news.ycombinator.com/item?id=7943303)이 있고, DailyJS.com에서 [요약](http://dailyjs.com/2014/06/26/optimization-killers/)되어있다.

### 2. 미지원 문법
현재까진 최적화되지 않는 문법 
- 제너레이터 함수 
- for-of를 포함하는 함수
- try-catch를 포함하는 함수
- try-finally를 포함하는 함수
- `let`이나 `const` 선언을 포함하는 함수
- `__proto__`나 `get`이나 `set` 선언이 있는 객체 표현식을 포함하는 함수

앞으로도 계속 안될 것 같은 문법 
- `debugger`문을 포함하는 함수 
- `eval()`을 호출하는 함수 
- `with` 문을 포함하는 함수

`try-catch-finally`에 대해서는 다음과 같이 함수로 뽑아서 우회 가능

```js
var errorObject = {value: null};  
function tryCatch(fn, ctx, args) {  
    try {
        return fn.apply(ctx, args);
    }
    catch(e) {
        errorObject.value = e;
        return errorObject;
    }
}

var result = tryCatch(mightThrow, void 0, [1,2,3]);  
//Unambiguously tells whether the call threw
if(result === errorObject) {  
    var error = errorObject.value;
}
else {  
    //result is the returned value
}
```

### 3. `arguments` 관리

#### 3.1. `arguments`를 함수 내에서 사용하면서 파라미터로 넘겨준 변수에 다른 값을 넣을 때

```js
function defaultArgsReassign(a, b) {  
     if (arguments.length < 2) b = 5;
}
```
우회
```js
function reAssignParam(a, b_) {  
    var b = b_;
    //unlike b_, b can safely be reassigned
    if (arguments.length < 2) b = 5;
}
```
혹은
```js
function reAssignParam(a, b) {  
    if (b === void 0) b = 5;
}
```

#### 3.2. `arguments`를 외부로 노출
```js
function leaksArguments1() {  
    return arguments;
}
function leaksArguments2() {  
    var args = [].slice.call(arguments);
}
function leaksArguments3() {  
    var a = arguments;
    return function() {
        return a;
    };
}
```
우회
```js
function doesntLeakArguments() {  
    var args = new Array(arguments.length);
    for(var i = 0; i < args.length; ++i) {
        args[i] = arguments[i];
    }
    return args;
}
```
혹은 빌드 과정에서 매크로를 사용해 치환하는 방법
```js
function doesntLeakArguments() {  
    INLINE_SLICE(args, arguments);
    return args;
}
```
bluebird에서 사용하는 방법
```js
function doesntLeakArguments() {  
    var $_len = arguments.length;var args = new Array($_len); for(var $_i = 0; $_i < $_len; ++$_i) {args[$_i] = arguments[$_i];}
    return args;
}
```

#### 3.3. arguments에 값을 넣을 때
```js
function assignToArguments() {  
    arguments = 3;
    return arguments;
}
```

이것만 사용 
- `arguments.length`
- 유효한 인덱스 `i`를 통해 `arguments[i]`
- `.length`없이 직접 사용하지마라. `x.apply(y, arguments)` 이건 괜찮다. `.slice`와 `Function#apply`는 특별하니까

### 4. switch-case
case 조건이 128개까지는 괜찮고 초과하면 최적화를 못한다.

### 5. for-in
#### 5.1. key가 지역변수가 아닐 때
```js
function nonLocalKey1() {  
    var obj = {}
    for(var key in obj);
    return function() {
        return key;
    };
}
```
혹은
```js
var key;  
function nonLocalKey2() {  
    var obj = {}
    for(key in obj);
}
```
외부로 노출하거나 해당 루프의 지역변수가 아니면 불가능.

#### 5.2. 순환하려는 객체가 "simple enumerable"이 아닐 때
##### 5.2.1. "hash table mode"의 객체
생성자 밖에서 동적으로 프로퍼티들을 추가하거나 삭제할 때 hash table mode가 된다.

##### 5.2.2. 프로토타입 체인에서 enumerable 프로퍼티를 가질 때
```js
Object.prototype.fn = function() {};  
```

이렇게 하면 모든 객체에서 for-in이 최적화되지 못한다. `Object.create(null)`만 예외. 필요하면 `Object.defineProperty`로 enumerable하지 않은 프로퍼티를 만들 수 있지만 비추. (역주, 이유는 나와있지 않음)

##### 5.2.3. 배열의 인덱스를 키값으로 사용할 때
```js
function iteratesOverArray() {  
    var arr = [1, 2, 3];
    for (var index in arr) {

    }
}
```
배열을 순환하고 싶으면 `Object.keys`를 사용하는게 좋고, 프로토타입 체인 전체를 돌고 싶다면 다음과 같이 key값을 따로 모아서 처리한다.
```js
function inheritedKeys(obj) {  
    var ret = [];
    for(var key in obj) {
        ret.push(key);
    }
    return ret;
}
```