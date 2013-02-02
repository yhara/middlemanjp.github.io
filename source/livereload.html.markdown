---
title: LiveReload
---

# LiveReload

Middleman は LiveReload 機能の公式拡張を提供しています。 gem で簡単にインストールします:

``` bash
gem install middleman-livereload
```

`middleman-livereload` を Gemfile や `config.rb` に追加, さらに次の行を追加。

``` ruby
activate :livereload
```

middleman の web サーバを再起動します。

``` bash
middleman server
```

ページ内容が変更されると自動的にブラウザがリロードされます。
