---
layout: post
title: にっき
date: 2015-11-18 00:18
---

{% include img src='https://farm1.staticflickr.com/781/23079280322_a6bcdb3040_b.jpg' caption='なんか見てて目が疲れるので書き直した' %}

## 気に入らなかったところ

- MDLのグリッドシステムが使いづらい
- 大して使わないナビゲーションバーが上部固定で邪魔くさかった
- 色が多い
  {% include img src='https://farm1.staticflickr.com/750/22753460729_c797efcf54_b.jpg' %}


## 変更したところ

- フレームワークをMDLからBootstrapへ戻した
    - んほおぉぉぉぉ♡`div.container`べんりすぎるのおぉぉぉぉ♡
- 今までlessの変更を検知して`style.css`をビルドしてたけど、変更がある度に`git add`するのは面倒だし、ビルド後のCSSをバージョン管理する必要はまったくないので、CSSのビルドをJekyllで行えるようにSCSS記法へ変更
    - Jekyllがページのビルド実行時に一緒に`_sass/*.scss`をビルドして`/css/style.css`へデプロイしてくれる
- `/index.html`を直近のポスト1つを表示から、投稿リストへ変更
- コメント欄の設置
