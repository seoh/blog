title: Sublime Text에서 swift파일 빌드하기 
date: 2014-06-25
tags:
- Sublime Text
- swift
---

#### 조건
- Sublime Text와 [Package Control](https://sublime.wbond.net/installation)이 설치되어 있을 것
- Swift 개발 환경이 설정되어있을 것

#### 순서
- Package Control을 통해 Swift 패키지 설치
- Tools > Build System > New Build System... 에서 다음과 같이 입력한다.

```json
{
    "cmd": ["swift", "-i", "$file"],
    "selector": "source.swift"
}
```

- 빌드 정보를 저장한다. (예, `Swift.sublime-build`)
- cmd + B로 빌드한다.

![]/images/build-swift-file-on-sublime-text/swift-sublime-build.png)

#### 부연 설명
Swift 패키지를 설치하면 문법 강조는 기본으로 지원하고 각종 Snippet을 지원해서 Xcode만큼은 아니더라도 편하게 작성할 수 있다.

![]/images/build-swift-file-on-sublime-text/swift-sublime-snippet.png)

[Swift 패키지 프로젝트](https://github.com/quiqueg/Swift-Sublime-Package)에서 패키지 파일들을 확인하면 보다 자세한 정보를 알 수 있다.