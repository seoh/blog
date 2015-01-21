title: 코딩 인터뷰 완전 분석 215쪽 문제 18.10 풀이
date: 2012/09/12 21:52
tags:
---

> 사전에 등장하고 길이가 같은 두 단어가 주어졌을 때, 한 번에 글자 하나만 바꾸어 한 단어를 다른 단어로 변환하는 프로그램을 작성하라. 변환 과정에서 만들어지는 각 단어도 사전에 있는 단어여야 한다.
[실행 예]
input : DAMP, LIKE
output: DAMP -> LAMP -> LIMP -> LIME -> LIKE
[사전 데이터]
네 글자 단어 - [word4](http://www.insightbook.co.kr/wp-content/uploads/2012/09/word4.txt)
다섯 글자 단어 - [word5](http://www.insightbook.co.kr/wp-content/uploads/2012/09/word5.txt)
 
> [심화 문제 - 풀지 않아도 됩니다]
심화문제 1: 가장 적은 수의 단어를 써서 변환하도록 프로그램을 작성해봅시다.
심화문제 2: 가장 많은 수의 단어를 써서 변환하도록 프로그램을 작성해봅시다. 단, 변환 과정에서 같은 단어가 두 번 나오면 안됩니다.


가장 단순한 접근.
모든 데이터를 메모리에 올려놓고, 앞에서부터 한글자씩 대입해서 사전에서 검색해보고 있으면 치환. 목적지에 도착할 때까지 반복. 혹시 한바퀴(문자열 길이)를 돌 때까지 치환할 단어가 없으면 찾을 수 없다고 판단. 일종의 Greedy로 답을 찾을 수는 있지만 가장 빠른 답인지 보장하지는 못한다.
```coffee
# synonym.coffee
v = process.argv
if v.length < 5
    process.exit -1
n = parseInt v[3]
src = v[4].toLowerCase()
dst = v[5].toLowerCase()

data = require('fs').readFileSync './word' + n + '.txt'
data = data.toString().split '\n'

if data.indexOf(dst) is -1
    console.log 'dst does not exist in data text'
    return 0

console.log src
while src isnt dst
    __ = src
    for i in [0...n]
        temp = src.substring(0, i) + dst.charAt(i) + src.substring(i+1,n)
        if __ isnt temp and data.indexOf(temp) isnt -1
            src = temp
            console.log "-> #{src}"
            if src is dst then return
    if __ is src
        console.log "IMPOSSIBLE"
        return 0
```
그리고 출력값

```
$ coffee synonym.coffee 4 damp like
damp
-> lamp
-> limp
-> lime
-> like
```

소스에서는 생략했지만, 6회(두번째 루프) 만에 찾았다.

Shorted Path나 Longest Path를 찾기 위해서는 edge에 index 정보를 기록하는 graph를 만들어야할꺼 같은데, 그러려면 모델링하는데만 O(n^2)만큼의 시간이 걸린다.

좀 더 좋은 방법을 찾아보자.
