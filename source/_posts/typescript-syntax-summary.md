title: typescript syntax summary
date: 2012-10-08 03:55
tags: typescript
---

Microsoft가 며칠전 typescript를 발표했다. 아마 CoffeeScript처럼 ECMA Script 표준에 본격적으로 뛰어들기 위한 포석이 아닐까 싶은데, 어찌되었건 현재로는 무려 major IDE(라고는 하지만 Visual studio)에서도 강력하게 지원하고, 사람들이 많이 쓰는 [Sublime Text2, Emacs, Vim에서도 플러그인으로 지원](http://blogs.msdn.com/b/interoperability/archive/2012/10/01/sublime-text-vi-emacs-typescript-enabled.aspx)된다. 심지어 며칠전에 추가했던 SyntaxHighlighter의 [typescript 버전](http://ianobermiller.com/blog/2012/10/03/typescript-syntax-highlighting-for-wordpress/)도 벌써 올라왔다. 여하튼 좋은지 아닌지는 일단 적당히 써본 다음에 판단할 수 있으니 발이라도 살짝 담궈보기 위해서 Language Specification 문서를 받아봤으나 읽기엔 너무 길어서 dictation 연습이나 할까 하고 [소개 동영상](http://channel9.msdn.com/posts/Anders-Hejlsberg-Introducing-TypeScript)을 보려고 시도했다...고 하지만 1분만에 포기하고 그냥 언급된 소스만 보면서 메모해놓은 것들을 정리해봤다.


### typescript syntax summary

variable type (declared like scala)
// number, string, bool

var name:type;
array expression.
x: string[]

: lambda expression.
() => retVal

: arg => retVal
(a: number, b: string) => a + b

interface declaration and usage 
```ts
interface Thing {
    a: number;
    b: string;
}

function process(x: Thing) {
    return x.a;
}
```

object argument is expressed …
```
function process(x: {a: number, b: string}) {
    return x.a;
}
```

and also expressed
```
function process(x: Thing) {
    return x.a;
}
```

optional property
```
interface Thing {
    a: number;
    b?: string;
}
```

method; function property
```
interface Thing {
    a: number;
    foo(s: string, n?: number): string
}
```

method overload ...
```
interface Thing {
    a: number;
    foo(s: string, n?: number): string;
    foo(n: number): number;
}

var process = function(x: Thing) {
    x.foo("abc",1)
    x.foo("abc")
    x.foo(1)
}
```

and also expressed
```
interface Thing {
    a: number;
    foo: {
        (s: string, n?: number): string;
        (n: number): number;
    };
}
```

you cannot use that way
```
interface Thing {
    a: number;
    foo: {
        (n?: number, s: string): string;
        (n: number): number;
    };
}
```

you may wanna use that, then compiler tells you
Optional parameters may only be followed by other optional parameters
```
{ (n?: number,s: string): string; (n: number): number; }
```

method overload groupping
```
interface Thing {
    a: number;
    foo: {
        (s: string): string;
        (n: number): number;
        data: any;
    };
}


var process = function(x: Thing) {
    x.foo(1)
    x.foo("abc")
    x.foo.data = 1
    x.foo.data = "abc"
}
```

pre-declared method overload: index method like array, constructor method
```
interface Thing {
    a: number;
    b: string;
    foo: {
        (s: string): string;
        (n: number): number;
        data: any;
    };
    new (s: string): Element;
    [index: number]: Date;
}
```

class definition
```
class Point {
    // property
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
    dist() { 
        return Math.sqrt(this.x*this.x + this.y*this.y);
    }
    static origin = new Point(0, 0);
}

var p = new Point(10, 10);
p.x = 10;
Point.origin.x;
```

private member: but not in real, just compiler tells "it can`t"
```
class Point {
    private color: string;
}
```
->
```
var Point = (function() {
    function Point(x, y) {
        this.color = 'red';
    }
})();
```

constructor with declaration
```
constructor( public x: number, public y: number)
```
constructor with default value
```
constructor( public x: number = 0, public y: number = 0)
```

inheritance // can be use an acceessor (can be duplicated declaration in scope)
```
class Point {
    constructor(public x: number, public y: number) {}
}
class Point3D extends Point {
    constructor(x: number, y: number, public z: number){
        super(x, y);
    }
}
```

auto-generated closure // compare next sources
```
class Tracker {
    count = 0;
    start() {
        // legacy expression
        var that = this
        window.onmousemove = function(e) {
            that.count++;
            console.log(that.count);
        }
    }
}
(new Tracker()).start();
```
and
```
class Tracker {
    count = 0;
    start() {
        // lambda expression
        window.onmousemove = e => {
            this.count++;
            console.log(this.count);
        }
    }
}

(new Tracker()).start();
```
module declaration
```
module Hierarchical.Module.Name.Is.Available {
    export class Point {}
    class PrivateClass {}
}

module Hierarchical.Module.Name.Is.Available {
    export var partialDeclare
}

var shortedPath = Hierarchical.Module.Name.Is.Available
var p = new shortedPath.Point
```

external module and function
```
// target.ts
export module target {
    export class Dog {
        bark = function() {
            return "bow wow";
        }
    }
}

export function version()  {
    return "v0.0.1";
}
```

```
// main.ts
import t = module("target")
console.log( (new t.target.Dog).bark() )
console.log( t.version() )
```
=>
```
(03:13:55) test seoh$ tsc main.ts -e
bow wow
v0.0.1
```

다른 소스를 보다보면 node.d.ts, jquery.d.ts같은 파일들이 있는데, declarations 옵션으로 컴파일을 하면 c/c++의 header와 같은 개념으로 decoration typescript(d.ts) 파일이 생성된다. 예를들어 위에 있는 target.ts를 컴파일하면
```
(03:50:16) test seoh$ tsc --declarations target.ts
(03:50:23) test seoh$ cat target.d.ts 
export module target {
    export class Dog {
        public bark: () => string;
    }
}
export function version(): string;
```
처럼 선언만 남게 된다.


요즘 만지작거리고 있는 스칼라와 문법이 흡사해서 재미있기도 하고, module, class 개념으로 큰 프로젝트에서 분업할 때 편하긴 할 것 같은데... 아직 어디에 써야할지는 감이 안온다. 다른거 만지다보면 누군가 이런저런 토픽을 던저줄테니 일단 받아먹을 준비 중.

\* 위에서 언급된 소스들은 module 부분을 제외하고 Microsoft의 동영상 소개에서 가져온 내용이니 해당 소스들에 대한 권리는 MS에게 있습니다.
