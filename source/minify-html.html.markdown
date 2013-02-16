---
title: HTML の圧縮
---

# HTML の圧縮

Middleman は HTML 出力を圧縮する公式拡張を提供しています。 gem で簡単にインストールします:

``` bash
gem install middleman-minify-html
```

Gemfile に `middleman-minify-html` を追加し, `config.rb` を開いて次を追加。
Add `middleman-minify-html` to your Gemfile, open your `config.rb` and add

``` ruby
activate :minify_html
```

middleman の web サーバを再起動します。

``` bash
middleman server
```

ソースを確認すると, HTML が圧縮されていることが確認できるはずです。
