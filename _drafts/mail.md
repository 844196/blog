---
layout: post
title: GitHubのContributions Calendarを取得する
---

「オクラホマりえしょん」コーナー宛　RN:ウエンコタンで漂着していたマネキンを入手on84
ステッカーと内定希望です

「村川梨衣[^1]の a りえしょんぷり～ず❤」、毎週楽しく聞いています！  
先日すこし困ったことがあったのでお悩み解決お願いします。

[^1]: 「ﾃﾞﾓﾍﾟﾁﾉｶﾞﾓｯﾄｶﾜｲｲﾖｰ」の人

GitHubのプロフィールページにある`Contributions`を動的に取得するようなページを作って、リスナーの動向を監視したいのですが、どうしたらいいと思いますか？

{% include img src='https://farm1.staticflickr.com/720/22749022754_a910643548_b.jpg' caption='草やばない？' %}

実はこの`Contributions`はSVGというベクターデータで表現されています。難しそうに聞こえますが、中身はXMLです。  
このSVGは<https://github.com/users/ユーザ名/contributions>を叩くと取得できます。

```html
$ curl -sL 'https://github.com/users/844196/contributions'
<svg width="721" height="110" class="js-calendar-graph-svg">
  <g transform="translate(20, 20)">
      <g transform="translate(0, 0)">
          <rect class="day" width="11" height="11" y="13" fill="#eeeeee" data-count="0" data-date="2015-03-09"/>
          ...
```

これはそのままHTMLに貼り付けることができます。

{% include img src='https://farm2.staticflickr.com/1691/25347050960_db110818a9_b.jpg' caption='取得したそのままのSVG' %}

このままでもいいですが、文字の色・大きさを本家からCSSを流用するといい感じになります。

```css
.js-calendar-graph-svg text {
  font-size: 10px;
  fill: #aaa;
}
```

{% include img src='https://farm2.staticflickr.com/1482/25523987912_cb0761f0ea_b.jpg' caption='調整済みSVG' %}

うまくいったかよ〜
SVGのレスポンシブ対応については[ここ](http://qiita.com/SFPGMR/items/f32855b9cf038f5654bd)を参照してみてください。

**番組ではリスナーの皆様からのお便りを募集しています！**
**宛先はお葉書の場合、〒<ruby>105<rt>いちれーごー</rt></ruby><ruby>-<rt>の</rt></ruby><ruby>8002<rt>はっせんに！</rt></ruby> 文化放送 超A&G+ 「村川梨衣の a りえしょんぷり～ず❤」まで**
**メールの場合はすべて小文字で、`rietion@joqr.net`までです。`rietion`のつづりは「りえしょん」です**

**また、この番組では[公式Twitter](https://twitter.com/rietion)と[公式Vine](https://vine.co/u/1192619650127683584)を展開しています。あわせてフォローよろしくお願いします。**

**それでは今夜はこの辺でお別れです。お相手は村川梨衣でした。**
**また来週も a りえしょんぷり～ず❤**

---

このお便りは おさむん家 Advent Calendar 2014 のメールです
