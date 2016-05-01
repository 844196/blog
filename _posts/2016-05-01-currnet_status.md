---
layout: post
title: 近況
date: 2016-05-01 18:20
---

お久しぶりです

## 最近

{% include img src='https://farm2.staticflickr.com/1502/25121947924_8279b2e86a_b.jpg' caption='面接の時、ネタ見せでn_cipherの引数チェック部分（インクルードするとクラス内でチェックメソッドが使えて、引数のバリデーションを指定できる）を見せてドヤったけど、反応よくなくて「落ちたかなぁ」と思いながら食べてたお好み焼き' %}

3月の頭にいろいろあって、某社のフォームからエントリーしたら通って[^1]、4月からは旭川を離れて札幌のPHPの会社で働いています。

[^1]: なんで通ったのか未だに謎。拾ってくれてありがとうございます。

4月からの1ヶ月は*「ひと月あげるからなんかWebアプリ書いてみて」*と言われ、ヒーコラ言いながらなんだかんだで3つ小さいアプリを書いてました。どこまで書いていいのか分からないのでこの話はこの辺で。

## 開発環境

<blockquote class="twitter-tweet tw-align-center" data-lang="ja"><p lang="ja" dir="ltr">archに戻りたい</p>&mdash; ボードウォーク (@844196) <a href="https://twitter.com/844196/status/722495198782074881">2016年4月19日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

3月末にmacbook proを就職祝いで買ってもらって、それをずっと使ってます。しばらくまともな回線を使えなさそう（使えなかった）で、Archが急に使えなくなるのはメンテできなくて怖いなーと思ったのもあって、まだ立ち上げてないです。

最近の家での開発はVagrant + Ubuntu14.04（trusty）の閉じた環境でやってます （`Vagrantfile`は[ここ](https://github.com/844196/devntu)）

`vagrant up`して15分待つと以下が落ちてきて、`192.168.33.10/`と`192.168.33.10/phpmyadmin/`が使えるようになったり、`vim`, `tmux`, `peco`の最新ビルド、ぼくのドットファイルがプロビジョニングされて、`vagrant ssh`すると自動で`tmux`が立ち上がったりします。

* apache2
* mysql5.6
    * rootパスワード: `root`
* phpmyadmin
* php7
    * composer
* ruby2.3
    * bundler
* nodejs
    * npm
    * bower
* git
* zsh
* paco
* apt以外
    * vim
    * tmux
    * peco
    * ドットファイル

会社で開発始める前に*「`Vagrantfile`書いてコミットしといてね」*って言われて、「やっぱそうなのか〜」と感動した次第です。最近だと当たり前なんだろうけど。使ってなかったら環境構築で死ぬとこだった。

{% include img src='https://farm2.staticflickr.com/1535/26653128432_4121719e8b_b.jpg' caption='かわいい' %}

あと会社で使っててよかったので、家用のモニターアームを買いました。縦はかなりいいんだけど、見上げないといけなくて首が疲れる。

## 進捗とか知見とか

Vue.js弄ったり、`Pacbundle`（`pacman`のシェルスクリプトラッパー）とか書いてました。

`Pacbundle`は2年位前にどっかの外人が書いてたっぽいんですが、そんなにべんりじゃなかったのでRubyっぽい構文で書けるようにしました。

```bash
# 注: シェルスクリプトです

Setup::Pacman.group 'package' <<end
  package.catalog_sync
  package.upgrade
end

Setup::Pacman.group 'toolkit' <<end
  toolkit.package_install vim
  toolkit.package_install git
  toolkit.package_install tmux
  toolkit.package_install zsh
end
```

```bash
$ pacbundle install
```

<blockquote class="twitter-tweet tw-align-center" data-lang="ja"><p lang="ja" dir="ltr">やったーーーーーーーーーーーーーーーー！！！！！！！！！！！！！！！！ <a href="https://t.co/B61PGu7LsZ">pic.twitter.com/B61PGu7LsZ</a></p>&mdash; ボードウォーク (@844196) <a href="https://twitter.com/844196/status/719525937595875328">2016年4月11日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

### こぼれ話

*「2016年にもなって`neobundle.vim`の話かよ」*と思うかもしれませんが、`neobundle.vim`には`bin/neoinstall`というシェルスクリプトが同梱されており、実行するとバッチモードで`vim`が起動し`vimrc`に書いてあるプラグインをインストールしてくれます。プロビジョニングの時にべんりなのでどうぞ。

## 週末

{% include img src='https://farm2.staticflickr.com/1711/26747095375_eb45eecb2c_b.jpg' %}

なんだかんだ言って、毎週どっかこっか行ってました。

## さいごに

{% include img src='https://pbs.twimg.com/media/BtcSFKpCQAAb73x.jpg:large' caption='最近の昼ごはんは、食べ放題のうまい棒か社内通貨で買えるカロリーメイトです' %}

生きてます。火曜〜金曜まで実家に帰省する予定です。
