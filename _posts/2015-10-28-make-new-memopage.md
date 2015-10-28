---
layout: post
title: にっき
date: 2015-10-28 01:05
---

![](https://farm6.staticflickr.com/5716/21900350874_b3ed17ca03_b.jpg)

メモ用のページをJekyllでゴリゴリつくった。

Jekyllの経験は[昔つくったLog](https://www.flickr.com/photos/s083027/21898974164/in/dateposted-public/)と[村まとめ](http://vill.osamu-net.hokkaido.jp/)だけなんだけど、テンプレートの仕組みをよく理解しないで書いたので、めちゃくちゃ汚い。今読んでもまったくわからない（ので、村まとめをフルスクラッチで作りなおしてくれる方募集中です）。

## モチベーション

[前書いた](http://844196.com/post/131889235589/%E3%81%AB%E3%81%A3%E3%81%8D)とおりなんだけど、投稿のために`master`ブランチにpushするので、Current streakを稼げるんじゃないかと思ったため。

## 方針

ローカルで使ってる自作メモ用CSSをそのまま当てて、最低限記事一覧をリストで見れるくらいにしとこうと思ったけど、見た目がしょぼいと使うモチベーションが起きないので、Qiitaを丸パクリ。

あとは、同じBootstrap使ってるんだから、[GistList](http://keepoff07.github.io/gistlist)[^fn1]よりはかっこ良くしたいなぁという無駄なライバル意識。

[^fn1]: GistListは今ある`body`要素全体を`div.container`に入れるだけでも、だいぶ変わると思うんだけど

## 使ったライブラリ等の一覧

- Bundle（依存関係の解決）
- Jekyll（[pages-gem](https://github.com/github/pages-gem)でGitHub側で動いてる環境をそのままローカルで再現できる）
- Less（CSSを手で一個ずつ書くのが面倒だった）
- Guard（後述するLessの自動コンパイルとか）
- Bootstrap（外枠）
- highlight.js（記事中のコードのシンタックスハイライト）
- Font Awesome（アイコンフォントセット）

## コーディング

アドベントでは受けが悪いのでカット。

基本的に、外枠はNuitterでの成果物をそのまま当てて、それプラス、ヘッダにある`編集履歴`とか`編集リクエストの作成`もちゃんと動くようにした（履歴はGitHubのファイルコミット履歴、編集リクエストはPR作成に飛ぶようにした）。

## いいとこ

[Redcarpet](https://github.com/vmg/redcarpet)のreadmeちゃんと読んだら、fenced_code以外に色々な拡張Markdown記法を有効に出来るみたいで、せっかくなのでGitHub準拠＋footnoteを有効にした[^fn2]。[Jemoji](https://github.com/jekyll/jemoji)も有効にしたので絵文字が使える:sushi: 。

[^fn2]: ただ、注釈に飛んで元の文に戻ると、navbarに被って元の文が見えない。なんとかしたい。

あと、[Guard::LiveReload](https://github.com/guard/guard-livereload)でライブリロード出来るようにした。Lessのコンパイルで元々使う予定だったけど、ライブリロードも出来るっぽいので入れておいた。previmとは違って必ず保存しないと更新されないけど、いちいちブラウザにフォーカスを移して`<C-r>`押すよりは楽。

最後に、リポジトリそれ自体がサイトの構成物全てだから、gemを`bundle install`してJekyllのプレビューサーバをローカルで立ち上げたら、ヘッダ・フッタ等含めたサイト全要素のライブプレビューができること。出来るのは当たり前と言えば当たり前なんだけど。

## 学び

- Bootstrapはだいぶ小慣れてきた感がある。ページトップの記事一覧で使った`.list-group`要素とか初めて使った。
- Font Awesomeは偉大。アイコンがあるのとないのでは画面の印象が全く違う。
