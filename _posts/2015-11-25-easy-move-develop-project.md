---
layout: post
title: プロジェクトルートへpecoで簡単に移動する
date: 2015-11-25 19:53
---

{% include img src='https://49.media.tumblr.com/933802783210add8559278831eb2235f/tumblr_nyda61dCWV1s7qf9xo1_540.gif' %}

`ghq`使えってツッコミはなしで...

```bash
alias gp='cd "$(find ~/dev -maxdepth 1 -type d | peco)"'
```
