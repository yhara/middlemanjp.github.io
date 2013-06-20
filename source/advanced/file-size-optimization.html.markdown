---
title: ファイルサイズの最適化
---

# ファイルサイズの最適化

## CSS と JavaScript の圧縮

Middleman は CSS のミニファイや JavaScript の圧縮処理を行うので, これについては心配する必要はありません。ほとんどのライブラリはデプロイを行うユーザのためにミニファイや圧縮されたバージョンを用意していますが, それらのファイルは読めなかったり編集できなかったりします。 Middleman はプロジェクトの中でオリジナルのコメントされたファイルを取っておくので, 必要に応じて読んだり編集したりできます。しかし, プロジェクトのビルド時には, Middleman は最適化の処理を行います。

`config.rb` で, サイトのビルド時に `minify_css` 機能と `minify_javascript` 機能を有効化にします。

``` ruby
configure :build do
  activate :minify_css
  activate :minify_javascript
end
```

ファイル名に `.min` を含む圧縮されたファイルを使用している場合, Middleman はそのファイルを処理しません。これは作者によって事前に注意深く圧縮された jQuery のようなライブラリにはとてもいいです。

`config.rb` で `:minify_javascript` 拡張を有効化している場合, `:compressor` オプションに Uglifier のカスタムインスタンスを設定することによって JavaScript の圧縮処理の方法をカスタマイズできます。詳細は [Uglifier's docs](https://github.com/lautis/uglifier) 参照。例えば, 次のように危険な最適化やひどい top-level の変数名を有効化できます:

``` ruby
set :js_compressor, Uglifier.new(:toplevel => true, :unsafe => true)
```

一部のファイルをミニファイ処理から除外するには, これらの拡張を有効化する際に `:ignore` オプションを渡し, 無視するファイルを識別する 1 つ以上のパターンマッチ, 正規表現や Proc を与えます。同様に, ファイル拡張子をリネームし変更するために `:exts` オプションを渡すことができます。

You can speed up your JavaScript minification (and CoffeeScript builds) by including these gems in your `Gemfile`:

```ruby
gem 'therubyracer' # faster JS compiles
gem 'oj' # faster JS compiles
```

## GZIP のテキストファイル

対応するユーザエージェントに [圧縮ファイルを配信する](http://developer.yahoo.com/performance/rules.html#gzip) ことはいい考えです。多くの web サーバはオンザフライでファイルを gzip にする機能を持っていますが, ファイルが配信される度に CPU を動作させる必要があり, 結果としてほとんどのサーバでは最大圧縮が実行されません。 Middleman は通常のファイルと一緒に gzip バージョンの HTML, CSS や JavaScript を作成することができ,  web サーバに pre-gzip ファイルを直接配信するように命じることができます。まず,  `:gzip` 拡張を有効化します:

``` ruby
activate :gzip
```

この後, それらのファイルを配信するようにサーバを設定します。 Nginx を使用する場合, [gzip_static](http://wiki.nginx.org/NginxHttpGzipStaticModule) モジュールを確認してください。 Apache の場合, 少しトリッキーなことをしなければなりません - 例として [Gist](https://gist.github.com/2200790) を確認してください。

## 画像圧縮

ビルド時に画像も圧縮したい場合, [`middleman-imageoptim`](https://github.com/plasticine/middleman-imageoptim) を試してみましょう。

## HTML 圧縮

Middleman は HTML 出力を圧縮する公式拡張を提供しています。gem で簡単にインストール:

``` bash
gem install middleman-minify-html
```

Gemfile に `middleman-minify-html` を追加し, `config.rb` で有効化:

``` ruby
activate :minify_html
```

ソースを確認すると圧縮されていることがわかると思います。
