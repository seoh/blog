title: 쉘에서 현재 위치의 repository 정보 추가하기
date: 2014-06-23
tags:
- vcprompt
- shell
---

# GitHub에서 공개한 Bash/Zsh/Tcsh용 Prompt
- GitHub: [git / contrib / completion /](https://github.com/git/git/blob/master/contrib/completion/)
- 기본 구조: (<브랜치 이름>)
- 설명: [Put Your Git Branch in Your Bash Prompt](http://code-worrier.com/blog/git-branch-in-bash-prompt/)

### 한줄 추가로 현재 브랜치 표시
기본 구조: (<브랜치 이름>)
설명: [명령 프롬프트에 현재 Git 브랜치 표시하기](http://ccoroom.tumblr.com/post/14261727932/git)

### 현재 브랜치와 컬러 표시
기본 구조: (<브랜치 이름>)
설명: [bash 프롬프트에 Git와 Mercurial의 branch를 표시하기](http://blog.outsider.ne.kr/616)
비고: Mercurial도 가능.

### vcprompt를 이용한 프롬프트
설명: [vcprompt를 이용해서 bash 프롬프트에 VCS 정보 표시하기](http://blog.outsider.ne.kr/737)
기본 구조: (<형상관리 이름>:<브랜치 이름> )
비고: VCS 이름, commit hash, branch, revision, modified file, not-added 등 각종 변수 지원

### Bash prompot에서 Git 정보 보기
GitHub: [magicmonty/bash-git-prompt](https://github.com/magicmonty/bash-git-prompt)
기본 구조: (<브랜치 이름> <브랜치 상태>|<로컬 상태>)
설명: [리눅스 bash shell에서 git 상태 정보 보기](http://resoneit.blogspot.kr/2014/01/bash-git.html)
비고: add, conflict, modified, non-added, stashed, yet-pushed, yet-pulled 상태의 숫자 등 표시
결론
vcprompt를 이용했다. 이유는 homebrew로 설치 가능한 유일한 방법이라 설치하기가 쉬워서. 이전의 설정은 다음과 같다.

```bash
export PS1="$C_LIGHTGRAY(\t)$C_RESET \W \u $C_GRAY$ $C_RESET"  
```
즉, ((시간) 사용자 현재디렉토리 $ )의 구조였다. 여기에서 vcprompt를 추가해서

```bash
VC=" \$(vcprompt -f "$C_BG_GREEN%n:%b$C_BG_RED%m%u$C_RESET")"  
export PS1="$C_LIGHTGRAY(\t)$C_RESET \W \u$VC $C_GRAY$ $C_RESET"  
```
((시간) 사용자 현재디렉토리 형상관리정보 $)의 구조로 변경했다.



![](/images/add-current-repository-info-to-shell/shell.png)
위와 같이 전/후가 변경되었다.

사용된 색상관련 변수는 [터미널에서 프롬프트(Prompt)에 나타나는 정보와 색상 변경하기](http://blog.saltfactory.net/99)를 참고하면 된다.

