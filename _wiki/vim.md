---
layout  : wiki
title   : vim
summary : vim 배우면서 찾은 vim 사용팁들 정리
date    : 2020-06-03T09:47:23+0900
updated : 2022-07-20 22:50:34 +0900
tag     : vim
toc     : true
public  : true
parent  : [[Tools]]
latex   : false
---
* TOC
{:toc}

## vimwiki 작성팁

- 문서 작성시간 삽입(wiki 생성시 메타 정보 자동 입력이 안되서 수동으로 넣는중) `:put =strftime('%FT%T%z')`
- 

## vim 플러그인

vim 플러그인은 잘 쓰는 사람이 설정한걸 따라만 써도 반은 먹고 들어가는것 같다.

- [commentary.vim](https://github.com/tpope/vim-commentary)

	- `gcc` 라인 주석
	- `gc + motion` 타겟 모션을 주석 ex) `gcap` paragraph 주석 
	- `:7,17Commentary` 7~17 line 주석
	- `:g/TODO/Commentary` 모든 TODO를 주석
	- `gc` visual mode 에서 블록 주석
	- `gcgc` 주석 해제(토글)

- [surround.vim](https://github.com/tpope/vim-surround)

	- 마크다운 사용할 때, 문자열 넣을때, HTML태그 추가할 때 너무 유용한듯
	- `cs` surrounding을 변경
	- `cs'<q>` '를 <q>로 변경
	- `ds` surrounding을 제거
	- `ds"` "를 제거
	- `ys` surrounding을 추가
	- `ysiw]` 또는 `ysaw]` 또는 `ys1w]` word 1개를 대괄호로 감싸기 `ysaw`의 경우 중간에 삽입 모드로 바뀌는 경우가 있었다. (neovim만 그런건지?)
	- `yssb` 또는 `yss)` 라인 전체를 괄호로 감싸기
	- 비주얼모드에서 태그로 감싸기 `S<p class="important">`

- [textobj-entire](https://github.com/kana/vim-textobj-entire)

	- 버퍼에서 텍스트 전체를 선택할 때 사용하는건데 키 타이핑을 줄여준다. (못외워서 못쓸까봐 걱정)
	- neovim에서는 사용할 수 없는것 같다.
	- `ae` 현재 버퍼의 컨텐츠 전체 선택
	- `ie` `ae`와 비슷하지만 시작과 끝의 빈 라인을 제외하고 선택한다.

- [ReplaceWithRegister](https://github.com/vim-scripts/ReplaceWithRegister)

	- vim 사용하면서 불편하다고 생각했던 것이다. yank한 뒤에 붙여넣을 곳에 텍스트를 삭제하고 붙여넣기하면 기존 텍스트가 다시 삽입되는 일이 발생한다. delete가 실제로는 잘라내기처럼 작동하기 때문이다. 블랙홀 레지스터에 삭제하고 붙여넣으면 된다고 한다. 레지스터에 대해서 이해해야 쓸 수 있을것 같다.

## vim script
`:h usr_41.txt`  
[Learn Vimscript the Hard Way](https://learnvimscriptthehardway.stevelosh.com/)  
Add `!` to the end of functions, allowing them to reload.  
`:so` is the same as `:source`  
`:source %` will load the current file  

```
:h :eval
:h :execute
:h :expand
:h :let
:h :normal
:h :set
:h abbrev
:h autocmd
:h command
:h echo
:h expand
:h function
:h g:
:h line
:h map.txt
:h map
:h netrw
:h normal
:h scriptease
:h so
:h system
:h usr_41.txt
:h variables
```

## netrw
Practical Vim Tip 43 참조

## 레퍼런스
- [https://vim.fandom.com/wiki/Insert_current_date_or_time]
- https://github.com/JetBrains/ideavim/blob/master/doc/emulated-plugins.md


