---
layout: post
title: 'コマちゃん（http://keepoff07.github.io/cmd-chan/）の裏側'
date: 2016-02-19 15:42
---

{% include img src='https://pbs.twimg.com/media/CXSiz0NVAAAr5_1.png' caption='ぐへへ… 下から覗いたらスカート見えねぇかなぁ…' %}

## 全体的な枠組み

### 親ページ

```html
<body>
  <div id="container" class="container">
    <header>
      ...
    </header>

    <div id="content">
      <iframe src="cbp-src/html/top.html" id="disp" width="100%" height="0px" frameborder="0" scrolling="no"></iframe>
    </div>

    <footer>
      Copyright (C) 2015 CommandBlockProject all rights reserved.
    </footer>
  </div>
</body>
```

`cmd-chan`では各ページごとに1枚の完結したHTMLファイルを置くのではなく、`/index.html`内の`iframe#disp`の属性値をパラメータに応じて動的に書き換えることで、テンプレートエンジン的な挙動を実現しています。

{% include img src='https://farm2.staticflickr.com/1637/24492746734_8588ed66bc_b.jpg' %}

表示する内容の決定と属性値の書き換えは`head`で読み込まれている`CMCBasicMain.js`で行われています。

```javascript
// URLと一緒に渡されたパラメータを取得
// もうちょっと下に'Query'関数の定義がある
var parm = new Query;

$(function() {

  // 省略

  $(function() {
    // URLにパラメータが渡された場合
    if(parm.has) {
      // '?page=○○'を取得
      var page = parm.get('page');
      // パラメータで指定されたページがナビゲーションバーに定義されている（存在する）場合
      if(page != null && $('header ul #'+page).length) {
        // iframeのソースを変更
        document.getElementById("disp").src = "cbp-src/html/"+page+".html"+location.search+location.hash;
      }
    }
  });

  // 省略

});
```

パラメータで渡されたページ名が妥当かどうかは、`$('header ul #' + page).length`で行われています。最初見たときは「？」でしたが、実際に試すとわかります。

{% include img src='https://farm2.staticflickr.com/1528/24755260949_2bf61e4c80.jpg' caption='id値の要素が配列で返却されるので、その要素数で判定している' %}

### 子ページ

iframe内で表示されるページが決定し子ページが読み込まれると、子ページで`CMCBasicSub.js`が読み込まれます。このスクリプト群は主に親ページのDOM操作、

* ページのタイトルを書き換え（ブラウザの上に表示されるやつ）
* 子ページのレンダリングが完了したら、自身を表示するiframeの高さを適切な値に書き換え

を行います。

```javascript
$(function() {

  // 省略

  // ページ読み込みが完了した場合
  $(window).load(function(){
    // 親ページのiframeタグの高さ属性値を、自身の高さに合わせる
    window.parent.$('#disp').css('height', $('body').height());
  });

  var patit = window.parent.$('title');
  var befor = patit.text();
  // '親ページのタイトル | 子ページのタイトル'にページタイトルを変更する
  patit.text(befor+ " | " + $('title').text());

  // 省略
});
```

## この設計の利点

* 依存ツールがないため、オンライン[^1]かつブラウザがあればどの環境でも検証できる

## 問題点

1. DOMの書き換えをjsで行っているので、サブページが外部のクローラー等に対応できない
    * [Twitterカードがスタンプ用画像よりも優先的に表示されてしまう keepoff07/cmd-chan](https://github.com/keepoff07/cmd-chan/issues/1)
2. ページを追加する時は、ナビゲーションバーにリンクとidを追加しないと、正しく遷移しない（現行の設計上）
3. モバイルでの表示

[^1]: jQueryをCDN経由でロードしているため

[keepoff07/cmd-chan](https://github.com/keepoff07/cmd-chan)では1を解決できるスーパーハカーを募集しています！

## CC BY 4.0に基づく著作権表示

{% include img src='https://i.creativecommons.org/l/by/4.0/88x31.png' %}

[コマンドブロックちゃん](http://keepoff07.github.io/cmd-chan/)は[クリエイティブ・コモンズ 表示 4.0 国際 ライセンス](http://creativecommons.org/licenses/by/4.0/)の下で提供されています。
