---
layout: post
title: 1行目とそれ以降で別々の処理をする
date: 2016-02-06 09:29
---

## 問題

以下のようなテキストがある。Rubyスクリプトで読み込み、1行目をタイトル、それ以降をリストとして扱いたい。

### ファイル

```
買い物メモ
牛乳
パン
しょうゆ
```

### こうしたい

```ruby
wish_list = Memo.new('/path/to/memo')

wish_list.title
#=> "買い物メモ"

wish_list.items
#=> ["牛乳", "パン", "しょうゆ"]
```

## あってるけどちょっと違う

```ruby
class Memo
  attr_reader :title, :items

  def initialize(path)
    @items = []
    File.open(path) do |file|
      file.map(&:chomp).each.with_index(1) do |line, index|
        case index
        when 1
          @title = line
        else
          @items << line
        end
      end
    end
  end
end
```

```ruby
wish_list = Memo.new('/path/to/memo')

wish_list.title
#=> "買い物メモ"

wish_list.items
#=> ["牛乳", "パン", "しょうゆ"]
```

いや、あってるけどさ… `end`がキモい…

## 解答

`File`（`IO`）インスタンスオブジェクトの読み込みは、インスタンスが破壊的に操作される（説明しづらい）。

```ruby
File.open('/path/to/memo') do |file|
  p file.readline
  #=> "買い物メモ\n"

  file.each_line {|line| p line }
  #=> "牛乳\n"
  #=> "パン\n"
  #=> "しょうゆ\n"
end
```

なので、上記処理をそのままコンストラクタにすればよい。

```ruby
class Memo
  attr_reader :title, :items

  def initialize(path)
    File.open(path) do |file|
      @title = file.readline.chomp
      @items = file.map(&:chomp)
    end
  end
end
```
```ruby
wish_list = Memo.new('/path/to/memo')

wish_list.title
#=> "買い物メモ"

wish_list.items
#=> ["牛乳", "パン", "しょうゆ"]
```
