---
title: Rack ミドルウェア
---

# Rack ミドルウェア

Rack はオンザフライで内容を変更し, サーバ (Middleman) で処理される前にリクエストを傍受できるクラスの仕組みです。

Middleman は Middleman と連携するライブラリの広大な宇宙をを開く Rack ミドルウェアへの完全なアクセス権を持っています。

## 例: 構文ハイライト

このサイトは Middleman で書かれており, 構文ハイライトされた沢山のコードブロックからできています。構文ハイライトは Middleman の外で行われます。このサイトは `<code>` ブロックをレンダリングし, Rack ミドルウェアはこれらのブロックを引き継ぎ構文ハイライトを追加します。使用されたミドルウェアは [`Rack::Codehighlighter`](https://github.com/wbzyl/rack-codehighlighter) です。次が `config.rb` での使用方法です:

``` ruby
require 'rack/codehighlighter'
require "pygments"
use Rack::Codehighlighter, 
  :pygments,
  :element => "pre>code",
  :pattern => /\A:::([-_+\w]+)\s*\n/,
  :markdown => true
```

この処理を行うために `Gemfile` に正しい依存関係を追加して下さい:

``` ruby
gem "rack-codehighlighter", :git => "git://github.com/wbzyl/rack-codehighlighter.git"
gem "pygments.rb"
```

上記ブロックは `rack/codehighlighter` と `pygments.rb` ライブラリが必要です。 `use` コマンドは Middleman にこのミドルウェアを使用するよう命令します。残りは標準的な Rack ミドルウェアのセットアップで, 構文ハイライトを指示するミドルウェアに構文解析のために使用するバックエンドやコードブロックの検索方法をいくつか変数で渡します。

### ビルドサイクル

Rack ミドルウェアはビルドサイクルの間に行われたものを含むすべてのリクエストに対して実行されます。これは Rack ミドルウェアのプレビュー中の効果は, ビルドしたファイルに現れるということです。
しかし, プロジェクトがビルドされると静的なサイトになることに注意して下さい。サイトをビルドすると, Cookie, セッションや変数を期待してリクエストを処理する Rack ミドルウェアは動作しなくなります。

## 便利なミドルウェア

* [Rack::GoogleAnalytics]
* [Rack::Tidy]
* [Rack::Validate]
* [Rack::SpellCheck]

[Rack::GoogleAnalytics]: https://github.com/ambethia/rack-google_analytics
[Rack::Tidy]: https://github.com/rbialek/rack-tidy
[Rack::Validate]: https://gist.github.com/235715
[Rack::SpellCheck]: https://gist.github.com/235097
