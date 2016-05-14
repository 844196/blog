---
layout: post
title: うんこ
date: 2016-05-14 22:37
---

最近`vimrc`に足した設定

## linediff

{% include img src='https://66.media.tumblr.com/c3ab68774cf0aa9b3666e053c3395737/tumblr_o764a5CnYS1s7qf9xo4_r1_540.gif' %}

べんりそうだった

```nohighlight
NeoBundle 'AndrewRadev/linediff.vim'
```

*Ref.* [AndrewRadev/linediff.vim: A vim plugin to perform diffs on blocks of code](https://github.com/AndrewRadev/linediff.vim)

## Unite grep/git

{% include img src='https://67.media.tumblr.com/153a4c223812cb902f2df851c7521cfa/tumblr_o764a5CnYS1s7qf9xo2_540.gif' %}

*「どっかに`HogeFugaTrait::piyoPoyo()`ってのがあるはずだから、grepしてコード見ながら呼んで試してみて」*って言われた時用

```nohighlight
" <Leader>gでUnite grep
function! DispatchUniteGrepOrGit()
    if isdirectory(getcwd()."/.git")
        Unite grep/git:. -no-empty
    else
        Unite grep:. -no-empty
    endif
endfunction
nnoremap <Leader>g :<C-u>call DispatchUniteGrepOrGit()<CR>
```

*Ref.* [file_rec/asyncとfile_rec/gitを自動的に切り換える - Qiita](http://qiita.com/yuku_t/items/9263e6d9105ba972aea8)

## Unite file/git

{% include img src='https://66.media.tumblr.com/8e7766e136fd820be510b6652612e2eb/tumblr_o764a5CnYS1s7qf9xo3_540.gif' %}

*「`src`の下に`App\Model\Entity\UserActionType`みたいなのがあるはずだから、それ参考にしてみて」*って言われた時用

`Unite file`と違って、カレント以下を`.gitignore`を見ながら（多分`git ls-files`の実行結果）再帰的に掘ってくので、デカいリポジトリの場合は重い

```nohighlight
" <Leader><Leader>でUnite file
function! DispatchUniteFileRecAsyncOrGit()
    if isdirectory(getcwd()."/.git")
        Unite file_rec/git
    else
        Unite file_rec/async
    endif
endfunction
nnoremap <Leader><Leader> :<C-u>call DispatchUniteFileRecAsyncOrGit()<CR>
```

## *

{% include img src='https://66.media.tumblr.com/a950528f0a164f66484b43e356bebc5d/tumblr_o764a5CnYS1s7qf9xo1_540.gif' %}

<ruby>矩形<rt>くけい</rt></ruby>選択中に`*`を押すと検索してくれるやつ

素の`*`はどこまでが一語か分かりづらくて嫌い（haya14busa氏が作ったなんかがあるのは知ってるけど、プラグイン入れられない時でも使いたいし、これで十分なのでそのまま）

```nohighlight
" ビジュアルモードで選択中のテキストを検索
vnoremap * "zy:let @/ = @z<CR>n
```

*Ref.* [ちょっと便利なvimのTips - Webtech Walker](http://webtech-walker.com/archive/2009/01/18022957.html)

## 0と^の入れ替え

`^`とか配列考えたやつ何も考えてなかったろ

```
" 0と^を入れ替え
nnoremap 0 ^
nnoremap ^ 0
```
