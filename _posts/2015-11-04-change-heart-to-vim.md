---
layout: post
title: Twitterの「いいね」を任意のアイコンに変更する知見
date: 2015-11-04 18:40
tags: Twitter Vim CSS
---

<blockquote class="twitter-tweet tw-align-center" lang="ja"><p lang="ja" dir="ltr">ツールチップの文言変えられなかったけど、文字色変えたしいいや（投げやり） <a href="https://twitter.com/hashtag/vim?src=hash">#vim</a> <a href="https://t.co/IWPCC5V9VJ">pic.twitter.com/IWPCC5V9VJ</a></p>&mdash; はちよん (@844196) <a href="https://twitter.com/844196/status/661820819207643136">2015, 11月 4</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

## tl;dr

<div class="alert alert-info">おさむん<ruby>家<rt>ち</rt></ruby> <ruby>Advent<rt>あどべんと</rt></ruby> <ruby>Calendar<rt>かれんだー</rt></ruby>では、この<ruby>節<rt>せつ</rt></ruby><ruby>以外<rt>いがい</rt></ruby>を<ruby>読<rt>よ</rt></ruby>む<ruby>必要<rt>ひつよう</rt></ruby>はありません</div>

<ruby>Twitter<rt>ついったー</rt></ruby>の<ruby>仕様変更<rt>しようへんこう</rt></ruby>により、<ruby>今<rt>いま</rt></ruby>までの:star:（お<ruby>気<rt>き</rt></ruby>に<ruby>入<rt>い</rt></ruby>り）アイコンが:heart:（いいね）アイコンへ<ruby>変更<rt>へんこう</rt></ruby>されました。しかし、<ruby>気<rt>き</rt></ruby>に<ruby>入<rt>い</rt></ruby>らないユーザは、<ruby>自身<rt>じしん</rt></ruby>でスタイルシートを<ruby>作成<rt>さくせい</rt></ruby>・<ruby>適用<rt>てきよう</rt></ruby>することで<ruby>自分<rt>じぶん</rt></ruby>の<ruby>好<rt>す</rt></ruby>きなアイコンへ<ruby>変更<rt>へんこう</rt></ruby>することができます。

### <ruby>猿<rt>さる</ruby>にもわかるように<ruby>噛<rt>か</ruby>み<ruby>砕<rt>くだ</ruby>いた<ruby>解説<rt>かいせつ</ruby>

ッウウッウ！ッッッウキ！ッウキキウ！ッッウキキ！ッッウキキ！ッウキウキ！ッッウキウ！ッキキウウウウッキ！ッウウウキウウッッウ！ッッウッウッッッキウ！ッウッッウキッウッキ！ッッウウウッッウッッ！ッキキウウウウウキ！ッキキウウッウッッ！ッキキウウッウキウ！ッキッキッキウッッ！ッウウウキウウウウッ！ッキキウウウキッウ！ッキッキキキキキッ！ッキキウウウウッキ！キウッッ！ッッウキッ！ッッウキキ！ッウッキッ！ッッウキウ！キウッッ！ッウウキキッキウウウキ！ッキッキキッキッキ！ッッウッキキッキッキ！ッキキウウウウウキ！ッウウッッキウキウキ！ッキキウウッウキウ！ッウウキキッキウウッウ！ッキキウウキウッウ！ッキキウウキウッキ！ッキキウウキキウキ！ッキキウッキウッウ！ッキッキキッキキッ！キウッッ！ッウキッキ！ッウキウキ！ッウッキッ！ッッウキウ！ッッウキキ！キウッッ！ッウウキキッキウウウキ！ッキッキキッッキキ！ッキッキキッッキキ！ッキキウウウウッッ！ッウウキキッキウウッウ！ッキキウウキウッウ！ッキキウウキウッキ！ッキキウウキキウキ！ッキキウッキウッウ！ッキキウウウッキウ！ッウッッウキッウッキ！ッッウウウッッウッッ！ッキッキキキウキッ！ッキキウウッウキキ！ッキキウウウキッウ！ッキッキキキッウウ！ッキッキキキッキキ！ッキッキッキウッキ！ッキッキキキッウウ！ッキッキキッキキウ！ッキッキキキッウウ！ッキッキッキウッッ！ッッウッキキッキッキ！ッキキウウウウウキ！ッウウッッキウキウキ！ッキキウウッウッキ！ッキキウウウウウッ！ッキッキキッッキキ！ッキキウッッッキキ！ッキキウッキッッウ！ッキキウウキキッキ！ッキキウウウウキウ！ッキッキッキウッッ！ッキウウッキッキッウ！ッキッキウウキキウッ！ッキッキキキキキッ！ッキキウウキキキキ！ッキキウッウウッキ！ッキキウウキウッキ！ッキキウッッキッッ！ッキキウウキキキウ！ッキキウッキッッウ！ッキキウッウッッキ！ッキキウウッッッキ！ッウウウキッキッッウ！ッウキッッウキキッウ！ッキキウッキッウキ！ッキッキキウッウキウ！ッッッキウッウキッッ！ッキッキキキッウキ！ッキキウウッウキッ！ッキッキキキウッキ！ッキッキキキキキキ！ッキッキキキキキッ！ッキウウッキッキッウ！ッウウッキッウキウッ！ッキキウウウウッキ！ッウッッッウキッッッ！ッキッキキッキキキ！ッキキウウウウウッ！ッキキウウキウッウ！ッキキウウキウッキ！ッキキウウキキウキ！ッキキウッキウッウ！ッキキウウウッキウ！ッウッッウキッウッキ！ッッウウウッッウッッ！ッキッキキキッウキ！ッキキウウッウキッ！ッキッキキキウッキ！ッキッキキキキキキ！ッキッキキッキキッ！ッキッキキキキキッ！ッキッキキッキキキ！ッキキウウウキッウ！ッキッキキキッウキ！ッキッキッキウッキ！

## ネタ元

> Twitterのふぁぼマークがハートになってしまったので、昔ながらの寿司アイコンに戻す方法を書いておきます。(Google Chromeのみ)
<cite>[Twitterのふぁぼマークを寿司にする - Qiita](http://qiita.com/GODVA_GOBBA/items/34fd127578ddb91dfd82)</cite>

ネタ元では、Twitter自体で持つ絵文字画像を参照し、通常時と`:hover`・`:focus`時で画像位置を変更する（`backgroud-position: right or left`）ことで:heart:から:sushi:への変更を実現している。

この考えを踏襲し、:heart:をVimのアイコンにする。

## 画像

オリジナルの`twitter_core.bundle.css`を見る限り、以下のような感じ。

- 画像幅: 50x50px
- 実際のアイコン幅: 16x16px

前述の通り、`backgroud-position`の変更によって、アイコンを変化させる。よって、画像の横幅は100（50*2）x50pxとなり、以下のような透過PNGとなる。

{% include img src="https://farm6.staticflickr.com/5657/22154408293_ba762b89dc_b.jpg" %}

### <i class="fa fa-upload fa-fw"></i> 画像のアップロード

CSS内で指定できるように、作成した画像をアップロードする。CORSを回避するため、画像はツイートに添付する形で、[pbs.twimg.com](http://pbs.twimg.com)にアップロードする。画像ファイルパスの取得方法はブラウザによって異なるが、Chromeであれば画像を右クリックし、`画像URLをコピー`で取得可能である。

{% include img src='https://pbs.twimg.com/media/CS8_YfTUsAANbq3.png' caption='実際にアップロードした画像( https://pbs.twimg.com/media/CS8_YfTUsAANbq3.png )' %}

## CSS

ネタ元を参考に、以下のように記述した。

```css
.HeartAnimation {
    background: url(https://pbs.twimg.com/media/CS8_YfTUsAANbq3.png);
    background-position: left;
}

.ProfileTweet-action--favorite .ProfileTweet-actionButton:hover .HeartAnimation,
.ProfileTweet-action--favorite .ProfileTweet-actionButton:focus .HeartAnimation,
.favorited .ProfileTweet-action--favorite .HeartAnimation {
    background-position: right;
}
```

## カスタムCSSの適用

ネタ元を参照せよ。

## SEE ALSO

- `yokunaine.md` created by @sasairc - <https://gist.github.com/sasairc/3af98b540784b73a136c>
    - Vim以外のアイコン集
