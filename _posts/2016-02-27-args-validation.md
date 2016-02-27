---
layout: post
title: メソッド引数チェックをDSLライクに行う
date: 2016-02-27 15:41
---

[NCipher](https://github.com/844196/n_cipher)をv0.5.0へアップデートしました。今まで同梱してた`n_cipher`コマンドは一時的に外しているのでご了承ください。

今回のアップデートは内部の無駄な記述をリファクタリング[^1]するのもありましたが、ここ2週間くらいローカルで行っていた実験を大きく反映させたものです。

[^1]: 特異メソッド生やすのにわざわざモジュールに入れて`extend`とか、あの時は頭が湧いてたとしか思えない。

## args_validation

NCipher v0.5.0では新しいメソッド引数チェックモジュールを同梱し、利用しています。

メソッド引数のバリデーション（文字数・型チェック）をする場合、

1. メソッド定義文の先頭で`if`チェック
2. 合ってたらスルー、間違っていたら例外を発生させる（もしくは`nil`なりを返す）

にどうしてもなってしまい、メソッド定義文が汚くなってしまいます。

```ruby
class User
  def password=(string)
    raise(ArgumentError) unless string.length >= 8 # ここが邪魔
    @password = string
  end
end
```

これを`attr_accessor`的なノリでバリデーションチェック出来る構文を追加するのが、`n_cipher/args_validation`モジュールです。

```ruby
require 'n_cipher'

class User
  include NCipher::ArgumentValidation

  # 処理がこれだけなら `attr_writer`に任せてもいい
  def password=(string)
    @password = string
  end

  args_validation :password= do |string|
    string.length >= 8
  end
end
```

バリデーション違反の際の例外メッセージを追加することもできます。バックトレースが`args_validation`のコンテキストと行番号であることにも注目してください。

```ruby
args_validation :password=, 'パスワードは8文字以上ある必要があります' do |string|
  string.length >= 8
end
```

```ruby
User.new.password = '1234567'
#=> /tmp/vk8ca1u/42:9:in `args_validation': パスワードは8文字以上ある必要があります (ArgumentError)
```

また、`args_validation`に渡すブロックのコンテキストはインスタンス内です。これによってインスタンス変数とのマッチングも可能です。

今までの分も含めた単体テストを用意し、引数が`@password`と一致していればログインできるメソッドを追加します。

```ruby
require 'user' # テスト対象
require 'rspec'
require 'rspec/autorun'
RSpec.configure {|config| config.formatter = 'doc' }

describe User do
  describe '#password=' do
    context 'パスワードが8文字未満の場合' do
      it { expect { User.new.password = '1234567' }
        .to raise_error(ArgumentError, 'パスワードは8文字以上ある必要があります') }
    end

    context 'パスワードが8文字以上の場合' do
      it { expect { User.new.password = '12345678' }.not_to raise_error }
    end
  end

  describe '#login' do
    before { @user = User.new.tap {|u| u.password = '12345678' } }

    context 'パスワードが合っていた場合' do
      it { expect(@user.login('12345678')).to eq('Login!') }
    end

    context 'パスワードが間違っていた場合' do
      it { expect { @user.login('1234567') }
        .to raise_error(ArgumentError, 'パスワードが間違っています') }
    end
  end
end
```

```ruby
require 'n_cipher'

class User
  include NCipher::ArgumentValidation

  def password=(string)
    @password = string
  end

  def login(string)
    'Login!'
  end

  # method argument validations

  args_validation :password=, 'パスワードは8文字以上ある必要があります' do |string|
    string.length >= 8
  end

  args_validation :login, 'パスワードが間違っています' do |string|
    string == @password
  end
end
```

```ruby
User
  #password=
    パスワードが8文字未満の場合
      should raise ArgumentError with "パスワードは8文字以上ある必要があります"
    パスワードが8文字以上の場合
      should not raise Exception
  #login
    パスワードが合っていた場合
      should eq "Login!"
    パスワードが間違っていた場合
      should raise ArgumentError with "パスワードが間違っています"

Finished in 0.00249 seconds (files took 0.08732 seconds to load)
4 examples, 0 failures
```

### メリット

* メソッド定義文がきれいになる
    - 前述したとおり
* バリデーションがドキュメントっぽく読める
    - 一番はここかな、と思ってる

## v.s.

＞　もうある　＜

* [gogotanaka/Rubype](http://gogotanaka.github.io/rubype.github.io/)
    - シンタックス（公式サイトより）

        ```ruby
        def sum(x, y)
          x.to_i + y
        end
        typesig :sum, [:to_i, Numeric] => Numeric
        ```

* [egonSchiele/constracts.ruby](http://egonschiele.github.io/contracts.ruby/)
    - シンタックス（公式サイトより）

        ```ruby
        Contract Contracts::Num, Contracts::Num => Contracts::Num
        def add(a, b)
          a + b
        end
        ```

こっちはブロックの評価をする汎用的なものということで、ひとつ…
