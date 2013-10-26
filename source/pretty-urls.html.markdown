---
title: きれいな URL (ディレクトリインデックス)
---

# きれいな URL (ディレクトリインデックス)

デフォルトで, Middleman はプロジェクトの中であなたが記述したとおりに正確にファイルを出力します。例えば, `source` フォルダの中の `about-us.html.erb` ファイルはプロジェクトのビルド時に `about-us.html` として出力されます。 `example.com` の web サーバのルートディレクトリにプロジェクトを配置すれば, このページは次 URL でアクセスできます: `http://example.com/about-us.html`



Middleman は `.html` 毎にフォルダを作成しそのフォルダの index としてテンプレートを構築するディレクトリインデックス拡張を提供しています。`config.rb` で次のように:

``` ruby
activate :directory_indexes
```

このプロジェクトがビルドされた時,  `about-us.html.erb` ファイルは `about-us/index.html` として出力されます。"index ファイル" 対応の web サーバに配置された場合 (Apache や Amazon S3), このページは次の URL でアクセスできます:

``` ruby
http://example.com/about-us
```

別のファイル名で出力したい場合, `index_file` 変数が使用できます。例えば,  IIS では default.html が使用されます:

``` ruby
set :index_file, "default.html"
```

もしくは, PHP ファイルにしたい場合:

``` ruby
set :index_file, "index.php"
```

#### アセットパスに関する注意事項

ディレクトリインデックスを使用する場合, 画像ファイル名だけでアセットファイルの呼び出し (例: 画像ファイル) を行うと失敗します。次のように完全な抽象パスを使って呼び出される必要があります:

``` ruby
![すごい画像](/posts/2013-09-23-some-interesting-post/amazing-image.png)
```

わずかにこのプロセスを自動化するには, MarkDown をまずは ERB で作成します。例えば, `/posts/2013-09-23-some-interesting-post.html.markdown.erb` ファイルがあるとします:

``` ruby
![すごい画像](<%= current_page.url %>some-image.png)
```

## オプトアウト

自動的に名前を変更したくないページがある場合, 除外できます:

``` ruby
page "/i-really-want-the-extension.html", :directory_index => false
```

1 度に沢山のファイルのインデックスをオフにしたい場合は, `page` には正規表現かファイルのパターンマッチを与えることができます。

ページ毎に [YAML 形式の Frontmatter](/frontmatter/) に `directory_index: false` を追加することもできます。

## 手動インデックス

テンプレートのファイル名が既に `index.html` の場合, Middleman は手をつけません。例えば, `my-page/index.html.erb` はあなたの予想どおり `my-page/index.html` と生成されます。
