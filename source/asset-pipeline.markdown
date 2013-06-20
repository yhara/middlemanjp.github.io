---
title: アセットパイプライン
---

# アセットパイプライン

## 依存性管理

[Sprockets] は Javascript (と CoffeeScript) のライブラリを管理するためのツールで, 依存性を宣言し, 3rd パーティのコードを含めます。Sprockets は .js や .coffee のファイルの中で,  `require` メソッドを使用できるようにし, プロジェクトまたは 3rd パーティ製の gem から外部ファイルを取り込むことができます。

jQuery ライブラリを含む `jquery.js` ファイルと別ファイルでアプリケーションコードが含まれる `app.js` があるとします。 次のようにすることで, app ファイルは jquery を含むことができます:

``` javascript
//= require "jquery"

$(document).ready(function() {
  $(".item").pluginCode({
    param1: true,
    param2: "maybe"
  });
});
```

このシステムは CSS ファイルの中でも機能します:

``` css
/*
 *= require base
 */

body {
  font-weight: bold;
}

```

Sass を使用する場合には, Sprockets の 機能よりも Sass の `@Import` を使用するべきです。

## アセット gem

次のように, `Gemfile` で読み込まれた gem からアセットを使用することができます:

```ruby
gem "bootstrap-sass", :require => false
```

`:require => false` はやや重要です。これらの多くの gem は Rails で実行するものと仮定しており, Rails や Compass 内部にフックしようとすると壊れます。gem を require することを回避し, Middleman は残りの面倒をみます。

一度これらの gem の依存関係を追加すると, gem から画像やフォントが自動的に読み込まれます。JavaScript や CSS はファイルの中で `require` や `@import` すると使用できます。

アセットファイルとして追加せず, HTML から 直接 gem のスタイルシートや JS ファイルを参照したい場合, `config.rb` の中で明示的に読み込む必要があります。

```ruby
sprockets.import_asset 'jquery-mobile'
```

`script` タグや `javascript_include_tag` から直接参照することができます。

## Sprockets にパスを追加

`:js_dir` や `:css_dir` の他にもアセットディレクトリがある場合, Sprockets にパスを追加することができます。`config.rb` に追加してください:

```ruby
sprockets.append_path '/my/shared/assets/'
```

## Compass

Middleman は柔軟な [Compass] サポートを備えています。Compass は Sass でクロスブラウザなスタイルシートを書くためのパワフルなフレームワークです。Compass は, [Susy] のように, それ自体が拡張で Middleman で使用できます。`image-url` のような Sprockets パスヘルパは Middleman のサイトマップにフックされるので, その他の拡張( :asset_hash のような) もスタイルシートに影響します。

[Sprockets]: https://github.com/sstephenson/sprockets
[Compass]: http://compass-style.org
