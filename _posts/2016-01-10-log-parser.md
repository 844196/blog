---
layout: post
title: 'あれぇ〜おかしいね。2016年なのに2011年度入学生がいるね？'
date: 2016-01-10 13:46
---

この記事は[おさむん家 Advent Calendar 2014](https://docs.google.com/spreadsheets/d/18s122iy_F8RgivDzF6BE6fL-nuHyOql5lZ3E_qeA1Dw/edit#gid=1701793155&vpid=A1)の記事です。**この記事は必ず読む必要があります。**

2016年初Post
年末から帰省してる間ずっと、頼まれてたログのパーサーを書いてた。そん時の知見。

## <i class="fa fa-road fa-fw"></i> 前提

以下、よく使うので

### `DATA`定数

`DATA`定数で`__END__`からファイル末尾までをFileオブジェクトとしてアクセスできる。

```ruby
p DATA.class

DATA.each_line do |line|
  p line.chomp
end

__END__
foo
bar
bazz
```

```
File
"foo"
"bar"
"bazz"
```

ヒアドキュメントの代わりに使ったりする。

### `Kernel.#pp`

既存の`Kernel.#p`（オブジェクトをリテラルのまま表示してくれる）を、改行とか入れて見やすくしてくれる

```ruby
require 'date'
require 'time'
require 'digest'

# これ
require 'pp'

class Session
  attr_reader :user, :pass, :date, :time

  def initialize(username, password)
    @username = username
    @password = Digest::SHA256.hexdigest(password)
    @time     = Time.now
  end
end

session = Session.new('844196', 'hogefuga')

puts "Kernel.#p"
p session

puts "\nKernel.#pp"
pp session
```

```
Kernel.#p
#<Session:0x000000015a7b60 @username="844196", @password="4cac15dfacf86b494af5f22ea6bdb24e1223bf2ef2d6718313a550ea290cda75", @time=2016-01-10 13:15:50 +0900>

Kernel.#pp
#<Session:0x000000015a7b60
 @password="4cac15dfacf86b494af5f22ea6bdb24e1223bf2ef2d6718313a550ea290cda75",
 @time=2016-01-10 13:15:50 +0900,
 @username="844196">
```

## <i class="fa fa-file-code-o fa-fw"></i> YAMLの読み込み

簡単だった。

```ruby
require 'yaml'

# Fileオブジェクトを渡す
p YAML.load(DATA)

# ファイルパスを渡したい場合
# YAML.load_file('/path/to/yaml')

__END__
foo:
  bar
hoge:
  fuga
```

```
{"foo"=>"bar", "hoge"=>"fuga"}
```

### オブジェクトの指定

`!ruby/{オブジェクト名}`のあとにリテラルを書くと、読み込む段階で指定のオブジェクトに変換してくれる。

```ruby
require 'yaml'

yaml = YAML.load(DATA)
p yaml
p yaml['regexp'].class

__END__
regexp:
  !ruby/regexp /\A\d+\s.+\z/
```

```
{"regexp"=>/\A\d+\s.+\z/}
Regexp
```

指定できるオブジェクトの種類は[ここ](http://docs.ruby-lang.org/ja/2.3.0/library/yaml.html)を参照

## <i class="fa fa-file-excel-o fa-fw"></i> CSVの読み込み

これも簡単だった。

```ruby
require 'csv'
require 'pp'

header = %i(no, name, amount)

# ファイルオブジェクトから（適当）
records = DATA.map do |line|
  header.zip(CSV.parse_line(line)).to_h
end

# ファイルパスから
# records = CSV.foreach('/path/to/csv') do |row|
#   header.zip(row).to_h
# end

pp records

__END__
1,"A","123,000"
2,"B","456,000"
3,"C","789,000"
```

```
[{:"no,"=>"1", :"name,"=>"A", :amount=>"123,000"},
 {:"no,"=>"2", :"name,"=>"B", :amount=>"456,000"},
 {:"no,"=>"3", :"name,"=>"C", :amount=>"789,000"}]
```

### `CSV::Converters`

`CSV.read`とか`CSV.parse`の`:headers`オプションに手続きオブジェクトを渡すと`CSV::FieldInfo`っていう構造体（みたいなの）が渡される。

```ruby
require 'csv'
require 'pp'

DATA.each_line do |line|
  pp CSV.parse(line, :converters => ->(content, info){ [content, info] }).first
end

__END__
2015/12/12,foo,bar,bazz
1993/03/08,hoge,fuga,piyo
```

```
[["2015/12/12", #<struct CSV::FieldInfo index=0, line=1, header=nil>],
 ["foo", #<struct CSV::FieldInfo index=1, line=1, header=nil>],
 ["bar", #<struct CSV::FieldInfo index=2, line=1, header=nil>],
 ["bazz", #<struct CSV::FieldInfo index=3, line=1, header=nil>]]
[["1993/03/08", #<struct CSV::FieldInfo index=0, line=1, header=nil>],
 ["hoge", #<struct CSV::FieldInfo index=1, line=1, header=nil>],
 ["fuga", #<struct CSV::FieldInfo index=2, line=1, header=nil>],
 ["piyo", #<struct CSV::FieldInfo index=3, line=1, header=nil>]]
```

これをcaseで分岐させていくと、読み込む段階で値を適切なオブジェクトに変換できてべんり。

```ruby
require 'csv'
require 'date'
require 'pp'

conv = ->(content, info) {
  case info.index #=> 0, 1, 2 ...
  when 0
    # 0（1）列目の値をDateオブジェクトへ変換
    Date.parse(content)
  when 1
    # 1（2）列目の値の後ろに「さん」を付ける
    "#{content}さん"
  else
    # それ以外の列はそのまま
    content
  end
}

DATA.each_line do |line|
  pp CSV.parse(line, :converters => conv).first
end

__END__
2015/12/12,foo,bar,bazz
1993/03/08,hoge,fuga,piyo
```

```
[#<Date: 2015-12-12 ((2457369j,0s,0n),+0s,2299161j)>, "fooさん", "bar", "bazz"]
[#<Date: 1993-03-08 ((2449055j,0s,0n),+0s,2299161j)>, "hogeさん", "fuga", "piyo"]
```

まぁ、`CSV.foreach`のループ内で好きなように変換すればいいだけなんだけどね...

（`Date`オブジェクトに変換するだけなら`:converters`に`:date`を渡すだけでいいらしいんだけど、`2000-01-01`はOKで`2000/01/01`はダメらしい。厳しすぎる。）

## <i class="fa fa-clock-o fa-fw"></i> `Time.parse`が重い

なんでか知らないが重い。[ここ](http://qiita.com/tkyowa/items/39d601976c13e9a9107e)の通り、`Time.local`を使うと3倍速くなった。

> ```
> local(year, mon = 1, day = 1, hour = 0, min = 0, sec = 0, usec = 0) -> Time
> mktime(year, mon = 1, day = 1, hour = 0, min = 0, sec = 0, usec = 0) -> Time
> ```
> 引数で指定した地方時の Time オブジェクトを返します。
> 第2引数以降に nil を指定した場合の値はその引数がとり得る最小の値です。
<cite><a href="http://docs.ruby-lang.org/ja/2.3.0/class/Time.html#S_LOCAL">class Time (Ruby 2.3.0)</a></cite>

`parse`に渡していた文字列オブジェクトを、そのまま`/`とか`:`で分割して配列にした上で、`*`で`local`の引数にすればいい。

```ruby
str = "2000/01/01 12:34:56".split(/[\/\s:]/)
#=> ["2000", "01", "01", "12", "34", "56"]

Time.local(*str)
#=> 2000-01-01 12:34:56 +0900
```

```ruby
require 'time'
require 'benchmark'

date = '2000/01/01'
time = '12:34:56'

Benchmark.bm 10 do |r|
  r.report 'Time.parse' do
    100000.times do |i|
      Time.parse("#{date} #{time}")
    end
  end

  r.report 'Time.local' do
    100000.times do |i|
      Time.local(*"#{date} #{time}".split(/[\/\s:]/))
    end
  end
end
```

```
                 user     system      total        real
Time.parse   3.120000   0.520000   3.640000 (  3.641309)
Time.local   0.960000   0.340000   1.300000 (  1.289687)
```

## <i class="fa fa-cutlery fa-fw"></i> `String#slice`と名前付きグループを含む正規表現オブジェクト

`String#slice`の引数に

1. 名前付きグループを含む正規表現オブジェクト
2. グループ名

を渡すと、グループ名にマッチした値をキャプチャできる。

```ruby
# 前提1:
#   /xをつけると正規表現リテラル内で改行できて見やすい
#   コメントや改行、スペースインデントは無視される
# 前提2:
#   (?<hoge>fuga)でグループに名前を付けられる
#   \1, \2とかより見やすい
#   String#gsubとかでマッチしたグループを参照したい場合は\k<hoge>で参照できる
regexp = /
  \A
  (?<faculty>a[khut])    # 学部コード
  (?<join_year>\d{1,2})  # 入学年度(2桁0詰め、ただし、2000〜2009年度入学生は先頭の0を省略可)
  (?<department>\d{2})   # 学科コード
  (?<range_number>\d{3}) # 連番(3桁0詰め)
  \z
/ix

user_id = 'ak1111054'
p user_id.slice(regexp, :join_year)
```

```
"11"
```

あれぇ〜おかしいね。2016年なのに2011年度入学生がいるね？

## <i class="fa fa-sitemap fa-fw"></i> 設計

面倒でも、種類ごとにクラスを切って、インスタンスに格納した方がわかりやすい。最初は1個の巨大なクラスで済ませようとして、拡張性がクソすぎた。

```ruby
module LogParser
  class Configuration
    # 設定値格納クラス
  end

  class << self
    # 設定メソッド
    # LogParser.configure, LogParser.config, LogParser.yaml_load
  end

  module Record
    # パーサー
    # LogParser.parse
  end

  module Record::Content
    # レコードオブジェクト

    class Base
      # レコード要素の抽象基底クラス
      # （現段階では全く使ってないけど、一応）フックメソッドを定義
    end

    class User < Base
      # ユーザIDを格納
      # 属性値、所属部署等を返却するインスタンスメソッドを定義
    end

    class PC < Base
      # クライアントIDを格納
      # PCの設置場所、種別等を返却するインスタンスメソッドを定義
    end

    class UseDateTime < Base
      # 使用日時を格納
      # 稼働時間等を返却するインスタンスメソッドを定義
    end
  end
end
```

## 感想

やっとオブジェクト指向っぽく書けるようになってきた
