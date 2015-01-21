title: 문제로 풀어보는 알고리즘 149쪽 문제 3.c 풀이
date: 2012-09-01 21:50
tags:
---

> 자연수 n을 입력받아 집합 {0, 1, 2, …, n-1}을 하나 이상의 집합으로 나누는 방법을 모두 출력하는 프로그램을 작성하세요.
[실행 예]
input n: 3
{0, 1, 2}
{0} {1, 2}
{1} {0, 2}
{2} {0, 1}
{0} {1} {2}
 
> * 참고 *
> 1. n의 범위는 크게 상관이 없지만, 대략 16 이하라고 가정하시면 되겠습니다. ^^
2. 집합으로 나눈 경우를 출력하는 방법은 상관없습니다.
- {1} {0, 2}를 {0, 2} {1}로 표현해도 되고, 1, 0 2로 표현해도 됩니다. (다른 형식도)
- 또, {0} {1, 2}가 먼저 출력되든, {0} {1} {2}가 먼저 출력되든 상관 없습니다. 빠짐없이 출력하기만 하면 됩니다.


곰곰히 생각해서 나름대로 잘 풀었다고 생각했는데 다른 사람들의 풀이를 보니 다 똑같아서 알고리즘의 설명은 생략하겠다. 그 대신에 잠깐 생각해본게 과연 이렇게 하나씩 추가하는 알고리즘에서 중복(순서가 바뀐 같은 집합)이 존재하지 않을까? 하는 의문이다. { ArrayN, ArrayM, ... } 이렇게 부분집합 n, m 이 있을 때 { ArrayM, ArrayN, ... } 라고 순서가 바뀐 다른 부분집합이 있을까? 답은 없다. n에서 제일 작은 n[0]과 m에서 제일 작은 m[0]가 있을 때 n[0] < m[0]라면 항상 n[0]가 앞에 있는 집합에 속해있다. 크기 순서대로 입력이 된다고 가정했을 때 m[0]가 n[0]보다 앞에 있으려면 n[0]보다 작은 숫자가 먼저 입력되어있는 부분 집합에 m[0]가 속해야하는데 그럼 그 집합에서 가장 작은 숫자가 m[0]가 아니기 때문에 모순되므로 m[0]가 n[0]보다 앞에 올 수 없어서 중복된 집합은 존재하지 않는다.


그리고 coffeescript를 실행할 때 compile하고 다시 node로 실행시키는 일을 두번 하기 싫은데 coffee에서 직접 파일을 입력받아 실행시키는 옵션은 없고 stdio로 script text를 받아서 실행시키는 옵션은 있어서 출력한 뒤 파이프로 넘기게 계속 명령어를 입력했는데 (cat test.coffee | coffee -s) argument를 입력받게 되니 coffee파일까지 커서를 옮겼다가 다시 argument로 커서를 옮겼다가 하기 귀찮아서 bash script 문서를 구글링해서 하나 만들었다.

``` bash
#!/bin/bash
if [ $# -lt 1 ]
then
    echo "you must give me coffee"
    exit
fi

cat $1 | coffee -s ${@:2}
```

중요한건 이게 아니고 다시 문제로 돌아가서 소스를 보자면

``` coffee
# subset.coffee
n = parseInt process.argv[3]

Array.prototype.clone = () ->
    ret = []
    for item in this
        item = item.clone() if item.constructor is Array
        ret.push item
    return ret

data = [ [[0]] ]

add = (arr, n) ->
    ret = []
    for row in arr
        for i in [0...row.length]
            (c = row.clone())[i].push n
            ret.push c
        row.push [n]
        ret.push row
    ret

for i in [1...n]
    data = add data, i

console.log data
```

그리고 실행

```
(21:58:11) coffee seoh$ ./c.sh subset.coffee 4
[ [ [ 0, 1, 2, 3 ] ],
  [ [ 0, 1, 2 ], [ 3 ] ],
  [ [ 0, 1, 3 ], [ 2 ] ],
  [ [ 0, 1 ], [ 2, 3 ] ],
  [ [ 0, 1 ], [ 2 ], [ 3 ] ],
  [ [ 0, 2, 3 ], [ 1 ] ],
  [ [ 0, 2 ], [ 1, 3 ] ],
  [ [ 0, 2 ], [ 1 ], [ 3 ] ],
  [ [ 0, 3 ], [ 1, 2 ] ],
  [ [ 0 ], [ 1, 2, 3 ] ],
  [ [ 0 ], [ 1, 2 ], [ 3 ] ],
  [ [ 0, 3 ], [ 1 ], [ 2 ] ],
  [ [ 0 ], [ 1, 3 ], [ 2 ] ],
  [ [ 0 ], [ 1 ], [ 2, 3 ] ],
  [ [ 0 ], [ 1 ], [ 2 ], [ 3 ] ] ]
```

cat test.coffee | coffee -s로 실행을 하면 내부적으로 어떤 구조로 node를 실행시키는지 모르겠지만 [ 'node', '.', '-s' ] 라는 세 argument가 기본적으로 붙는다. 그런데 -s는 help에도 안나오고 직접 node -s로 하면 unrecognized flag라고 나오고. 뭔지 모르겠지만 귀찮아서 패스.

사실 처음에는 다른 방법으로 접근했다가 포기했다. 어차피 모든 부분집합의 원소의 합은 n으로 일정하니 일단 그 크기를 나누는 것을 n=4일 때를 예로 들어,

4
3 + 1
2 + 2
2 + 1 + 1
1 + 1 + 1 + 1

로 나타낼 수 있다. 각 행마다 하나씩 예로 들자면

{ 0, 1, 2, 3 }
{ 0, 1, 2 }, { 3 }
{ 0, 1 }, { 2, 3 }
{ 0, 1 }, { 2 } , { 3 }
{ 0 }, { 1 }, { 2 }, { 3 }

로 나타낼 수 있다. 그래서

``` coffee
# split.coffee
n = parseInt process.argv[3]

split = (n) ->
    split.ret.clear
    split.arr.clear

    __split(n, 0, n)
    split.ret

split.arr = []
split.ret = []

__split = (n, i, m) ->
    if n is 0
        split.ret.push split.arr.slice(0, i)
        return
    
    if m > n
        m = n

    for j in [1 .. m]
        split.arr[i] = j
        __split n-j, i+1, j

n && console.log split n
exports && exports.split = split
```

라고 [1.6 수분할] 챕터를 참고해서 만들었다. 그런데 이걸 깔끔하게 정리해서 개수를 맞춰서 경우의 수를 만들 수 있는 방법을 못찾아서 다른 방법을 강구하게 되었다. 단순히 생각했을 때, memoization처럼 `memo['2.1.1']['2'].indexOf('1.2')`라는 식으로 2+1+1을 '2.1.1'로 {1, 2}는 '1.2'처럼 문자열로 만들어서 HashMap을 하나 만들면 구현은 편한데 어차피 모든 경우의 수를 만들고 거기에서 필터링하는거나 마찬가지라 (정확히 말하면 '가지치기'의 방법이 떠오르지 않아서) 비효율적이라 포기했다. 나중에 시간나면 지금처럼 bottom-up같은 방법이 아니라 top-down으로 할 수 있는 방법이 없나 찾아봐야겠다.
