---
layout: post
title: Oyaをリリースしました
date: 2016-02-12 05:56
---

<center>
  <iframe width="420" height="315" src="https://www.youtube.com/embed/fJ9rUzIMcZQ?rel=0" frameborder="0" allowfullscreen></iframe>
</center>

先日、マミーが10047歳の誕生日を迎えました。昔はちゃんと「お母さん」って言ってた気がするんですが、15を越えたくらいから恥ずかしさから「母ちゃん」とか「マミー」と呼ぶことが多くなった今日この頃です。お母さん、誕生日おめでとう。

祝辞はここまでとして、`Oya`というCLIツールをリリースしたので宣伝です。

## Oyaとは

{% include img src='https://cloud.githubusercontent.com/assets/4990822/12932330/a48e760c-cfc5-11e5-9b79-32532b348a53.png' %}

簡単に言ってしまえば、**ファイルの変更を検知し、任意のコマンドを実行するRubyスクリプト**です。こういった動きをするソフトウェアは`Guard`や`Grunt`がありますが、短いながらも設定ファイルを書かないといけないのが億劫（おっくう）でした[^1]。

[^1]: CLIで動く同種のソフトウェアとして`notify-tools`があるらしい。これは変更検知するAPIのみを提供っぽい？

いままでは、

```bash
$ while :; do command1; command2; done
```

などで誤魔化していましたが、[ファイルの変更を定期的に監視して、特定のコマンドを実行するシェルスクリプト（@tamanobi、Qiita）](http://qiita.com/tamanobi/items/74b62e25506af394eae5)を見て、簡単に作れそうなので作っちゃいました[^2]。

<center>
  <div class="github-card" data-github="844196/oya" data-width="400" data-height="150" data-theme="default"></div>
</center>
<script src="//cdn.jsdelivr.net/github-cards/latest/widget.js"></script>

[^2]: ねぇ、Polyanessは？

利用法としては、

* 書きなぐりのCLIツールのオプション引数のテスト
* ANSIエスケープシーケンスを駆使したcowfileの編集・確認

なんかで使えると思っています。

### 実行例

{% include img src='https://cloud.githubusercontent.com/assets/4990822/12924220/59689302-cf9c-11e5-9946-c359107440a0.gif' %}

`renge`の実行結果を`~/text`に追記（右ペイン）すると、`oya`が変更を検知して追記された行を表示している（左ペイン）のが確認できると思います。

## Oyaの裏側

### Oya::Watcher

一応、年始くらいからデザインパターンをやってるので[オブザーバ・パターン](https://ja.wikipedia.org/wiki/Observer_%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B3)で書きました。というか、挙動としてはそのまま当てはまりますね。

インスタンス生成時にファイルの変更通知を受け取りたいクラスをオブザーバとして登録しておけば、あとは`Oya::Watcher`が各ハンドラの`update`クラスを呼び出します。デザインパターンっていうと固いイメージですが、やってることはインターフェイスの強制というかポリモーフィズムぽいですね。

```ruby
# Oya::Watcherの一部
loop do
  # ファイルが変更されている場合は
  if target.changed?
    # オブザーバに通知する
    notify_handlers
  end
end
```

```ruby
# Oya::Watcherから通知を受け取るハンドラの例: シェル通知
class Oya::Handler::ShellNotifier < Oya::Handler::Base
  def initialize(message)
    @message = message
  end

  # Oya::Watcherから更新通知を受け取った場合は
  def show(params={})
    # `[時間] メッセージ' を表示する
    puts "\n[#{params[:time]}] #{(params[:message] || @message)}"
  end

  alias :update :show
end
```

通知先を増やしたい場合は、`Oya::Handler::Base`（定数参照用のベースクラス）を継承したクラスを生成し、`update`で`Oya::Watcher`からの通知を受け取れるようにすればよく、`Oya::Watcher`側で改修作業をする必要がありません[^6]。

[^6]: 理想

私は光学ドライブを持ってないので実装も確認もできませんが、*「ファイルが変更されたらドライブをエジェクトする」オブザーバ*を追加するPR待ってます。

### ライブラリAPIのインターフェイス

Rubyを始めて8ヶ月目、Gemを5個くらいしかリリースしてないぺーぺーなので毎回悩みます。

```ruby
watch = Oya.watch(target_path) do
  # 更新検知間隔をオプション引数で指定された値に設定
  interval = option[:interval]

  # 通知を受け取るオブザーバを追加
  add_handler Oya::Handler::ShellNotifier.new('Target update!')
  add_handler Oya::Handler::DesktopNotifier.new('Target update!')
  add_handler Oya::Handler::Command.new(command_str)
end
```

個人的には、コンストラクタにブロックを渡すと`self`をブロック引数にするような動きの方が好みです。ハンドラ登録が長くなりそうだったんで、`instance_eval`にしましたが…

### 外部設定ファイルの読み込み

[ここで書いた](http://qiita.com/844196/items/08cdc1e622861989941b#%E8%BF%BD%E8%A8%98-2016-02-11)んですが、`optparse`ライブラリのバグを踏んじゃったので、自前で実装しました。詳しくはリンク先と[実装](https://github.com/844196/oya/commit/5d3c17d6bb8f67642ce71e8b22902356ed856570)を見てください。

### Rakefile

gemspec（gemパッケージマネージャーが読み取るgemの基本情報や依存関係が記述されたファイル）を書く元気もなく依存する外部Gemもないので、インストールは`Rakefile`で`/usr/local/bin`へ直接コピーすることにしました。

一応、Ruby標準ライブラリの`un`（OS・ディストリビューション間の実装差異に関係なくコマンドを実行できる）を使っているので、`PREFIX`を正しく設定すれば、どこでもインストールできるはずです[^4]。

[^4]: コマンドライン引数のエンコード変換処理をしてないので、多分そのままでは動かないだろうけど

```bash
$ # `un' ライブラリ使用例、`mkdir' を実行している
$ ruby -run -e mkdir -- -p /usr/local/lib/oya
```

## 課題

### テストがない

{% include img src='http://blog.codinghorror.com/content/images/uploads/2007/03/6a0120a85dcdae970b0128776ff992970c-pi.png' caption='俺のマシンでは動いてるよ' %}

## 感想

`n_cipher`以来、まともなオレオレCLIツールを作ってなかった[^3]ので楽しかったです。[`renge`をリリースしたのがちょうど1年前くらい](http://loglogloglogloglog.tumblr.com/post/111071586949/%E3%81%9D%E3%81%97%E3%81%A6%E3%82%82%E3%81%A3%E3%81%8B%E3%81%84%E5%AE%9F%E8%A1%8C%E3%81%99%E3%82%8C%E3%81%B0%E3%81%86%E3%81%A1%E3%81%8C%E3%81%97%E3%82%83%E3%81%B9%E3%82%8B%E3%81%AE%E3%82%93)で、*「テキストファイルをランダム表示するだけのシェルスクリプト」*よりは成長した気もします。

[^3]: `log_zipper`とか仕事では作ったんだけどね
