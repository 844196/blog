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

## OpenStruct

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

## ref.

- class OpenStruct (Ruby 2.3.0) <http://docs.ruby-lang.org/ja/2.3.0/class/OpenStruct.html>
