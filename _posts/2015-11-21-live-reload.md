---
layout: post
title: Guardを用いたブラウザのライブリロード
date: 2015-11-21 10:44
---

<div class="alert alert-info">
  ひげ向け
</div>

{% include img src='https://farm1.staticflickr.com/771/22767992957_e5c5887b49_b.jpg' caption='ファイルが更新される度に、ブラウザをリロードしてくれます' %}

## <i class="fa fa-download fa-fw"></i> 準備編

1. Rubyとbundlerをインストールします

    ```bash
    $ # お好きなパッケージ管理ツールでRubyをインストール
    $ gem install bundler
    ```

2. `Gemfile`を用意します

    ```ruby
    source 'https://rubygems.org'

    group :development do
      gem 'guard'
      gem 'guard-shell'
      gem 'guard-livereload'
    end
    ```

3. gemをインストールします

    ```bash
    $ bundle install --path vendor/bundle
    ```

4. Guardがインストールされたか確認します

    ```bash
    $ bundle exec guard -v
    Guard version 2.13.0
    ```


## <i class="fa fa-gears fa-fw"></i> Guard設定編

1. `Guardfile`を用意します

    ```ruby
    guard :livereload do
      watch(%r{.*})
    end
    ```

2. Chrome拡張をインストールします
    - [LiveReload](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)


## <i class="fa fa-refresh fa-fw fa-spin"></i> 実行編

実行します

```bash
$ bundle exec guard start
```


## <i class="fa fa-lightbulb-o fa-fw"></i> 備考

ローカルのファイルを普通に`file://〜`で開いてると、`<head>`で読み込んでいる他のアセットファイル等が読み込めない問題が発生する。

Chromeを起動オプション付きで起動させる[^1]か、ローカルでサーバを立ち上げるとよい。

[^1]: <http://www.finefinefine.jp/howto/kiji861/>

### Rubyでローカルサーバ立ち上げ

```bash
$ ruby -r webrick -e 'WEBrick::HTTPServer.new(:DocumentRoot => "./", :Port => 8000).start'
```
