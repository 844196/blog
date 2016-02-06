---
layout: post
title: OpenStruct最高かよ
date: 2016-02-06 11:50
---

## method_missingを使った動的プロキシ

```ruby
require 'pp'

class Quote
  def initialize(hash)
    hash.each {|key, val| instance_variable_set("@#{key}", val) }
  end

  def method_missing(call_name, *args, &block)
    if instance_variable_defined?("@#{call_name}")
      instance_variable_get("@#{call_name}")
    else
      super
    end
  end

  def respond_to?(method_name)
    instance_variable_defined?("@#{method_name}") || super
  end
end

quotes = [
  {
    :speaker => 'マミー',
    :quote => '永山にチェスなんてあるわけないでしょ',
    :speaker_address => 'Oumu, Hokkaido, Japan'
  },
  {
    :speaker => 'アブドゥル',
    :quote => 'YES I AM!',
    :image => '/path/to/avdol.png'
  }
]

pp quotes.map {|quote| Quote.new(quote) }
```

```
[#<Quote:0x00000001cfbab0
  @quote="永山にチェスなんてあるわけないでしょ",
  @speaker="マミー",
  @speaker_address="Oumu, Hokkaido, Japan">,
 #<Quote:0x00000001cfb830
  @image="/path/to/avdol.png",
  @quote="YES I AM!",
  @speaker="アブドゥル">]
```

### インスタンス生成時

1. ハッシュのペアをイテレートし、インスタンス変数を宣言する

### インスタンスメソッド呼び出し時

1. インスタンス変数へのゲッターは定義していないのでオーバーライドした`method_missing`が呼び出される
2. 呼び出したメソッド名がインスタンス変数と一致する場合は、値を返却する
    - 宣言されていない場合は、本物の`method_missing`が呼び出される

## OpenStruct

最初に書いたやつとまったく同じ動きをする、Ruby同梱ライブラリ。

```ruby
require 'ostruct'
require 'pp'

quotes = [
  {
    :speaker => 'マミー',
    :quote => '永山にチェスなんてあるわけないでしょ',
    :speaker_address => 'Oumu, Hokkaido, Japan'
  },
  {
    :speaker => 'アブドゥル',
    :quote => 'YES I AM!',
    :image => '/path/to/avdol.png'
  }
]

pp quotes.map {|quote| OpenStruct.new(quote) }
```

```
[#<OpenStruct
  speaker="マミー",
  quote="永山にチェスなんてあるわけないでしょ",
  speaker_address="Oumu, Hokkaido, Japan">,
 #<OpenStruct
  speaker="アブドゥル",
  quote="YES I AM!",
  image="/path/to/avdol.png">]
```

`Struct`と同じく、継承することもできる。

```ruby
require 'ostruct'
require 'pp'

class Quote < OpenStruct
  def three_times
    3.times.map { quote }
  end
end

Quote.new(:speaker => 'アブドゥル', :quote => 'YES I AM!').three_times
#=> ["YES I AM!", "YES I AM!", "YES I AM!"]
```

### 難点

* セッター（`=(var)`）も定義される
* 定義していないキーにアクセスすると`nil`が返ってくる

## ref.

- class OpenStruct (Ruby 2.3.0) <http://docs.ruby-lang.org/ja/2.3.0/class/OpenStruct.html>
