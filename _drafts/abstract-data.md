---
layout: post
title: データの抽象レイヤー
date: 2016-05-17 01:49
---

まだ理解度が低い

## エンティティ

テーブル内の任意のレコード（1行）を抽象化したオブジェクト

```ruby
# ユーザーエンティティクラス
class User
end

user = User.new
```

### スケルトン

エンティティオブジェクトのプロパティ（列）とアクセサを定義する、エンティティの基底クラス

```ruby
# ユーザースケルトンクラス
class UserSkeleton
  def initialize
    # プロパティ
    @name = nil
  end

  # ゲッター
  def name
    self.name
  end

  # セッター
  def name=(value)
    self.name = value
  end
end

# ユーザーエンティティクラス
class User < UserSkeleton
end

user = User.new
user.name = 'foobar'
p user.name
#=> 'foobar'
```

## リポジトリ

テーブルを抽象化したクラス

実際のデータベースとの接続、値の取得・セットはここが行う（が、共通処理はtraitに記述し、必要に応じて`use`することで重複の排除とテストをしやすくする）
