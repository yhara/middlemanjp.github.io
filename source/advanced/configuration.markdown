---
title: 設定
---

# 設定

## Middleman の設定を知る

Middleman は信じられないほどカスタマイズ可能であり, 拡張はより多くの選択肢をもたらします。設定毎に徹底的なドキュメントを維持しようとするよりも, 私たちは Middleman にどの設定が可能なのか示す能力を与えました。

プレビューサーバを立ち上げ, `http://localhost:4567/__middleman/config/` を訪れると, すべての設定や可能な拡張を確認できます。設定名, 簡単な説明, デフォルト値やあなたのサイトではどう設定されているのかを含みます。

## 設定変更

一般的な設定変更の方法は `config.rb` で `set` を使用することです:

```ruby
set :js_dir, 'js'
```

やや新しい構文を使用することもできます:

```ruby
config[:js_dir] = 'js'
```

この方法は Middleman のグローバル設定のほとんどに使用されます。

## 拡張の設定

拡張は有効化された時に設定されます。ほとんどの拡張では, `activate` する際にハッシュのオプションを渡すか, オプションを設定するためにブロックを使います:

```ruby
activate :asset_hash, :exts => %w(.jpg) # Only hash for .jpg

# or:

activate :asset_hash do |opts|
  opts.exts += $(.ico)
end
```

## 特別な環境設定

ビルド時または開発環境で適用したい設定がある場合, ブロックの中で設定することができます:

```ruby
configure :development do
  set :debug_assets, true
end

configure :build do
  activate :minify_css
end
```
