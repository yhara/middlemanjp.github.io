---
title: きれいな URL (ディレクトリインデックス)
---

# きれいな URL (ディレクトリインデックス)

デフォルトで, Middleman はプロジェクトの中であなたが記述したとおりに正確にファイルを出力します。例えば, `source` フォルダの中の `about-us.html.erb` ファイルはプロジェクトのビルド時に `about-us.html` として出力されます。 `example.com` の web サーバのルートディレクトリにプロジェクトを配置すれば, このページは次 URL でアクセスできます: `http://example.com/about-us.html`

静的な web サイトにとってはリにかなっていますが, 多くの人は .html が不快だと思い, きれいな (わかりやすい) 拡張子なしの URL を好むでしょう。これに対応するには 2 つの方法があります。

## Ruby Web サーバ

Rack ベースの web サーバを使用しているなら, [rack-contrib] プロジェクトの `Rack::TryStatic` ミドルウェアを使うことができます。 `config.ru` の中に (もしくは Rails の Rack 設定), 次の行を追加してください:

``` ruby
require "rack/contrib/try_static"
use Rack::TryStatic, :root => "build", :urls => %w[/], :try => ['.html']
```

`about-us.html` と同じファイルは次の URL でアクセスできます: `http://example.com/about-us`

しかし, Rack を介してサイトを提供することは, やや静的サイトを生成する目的に反します。

## Apache (または互換サーバ)

Rack ベースの web サーバを使用していない場合, Middleman に `.html` 毎にフォルダを作成し, テンプレートファイルを index としてフォルダ内に作成するように命令するディレクトリインデックス機能を使いことができます。 `config.rb` に追加します:

``` ruby
activate :directory_indexes
```

このプロジェクトがビルドされた時,  `about-us.html.erb` ファイルは `about-us/index.html` として出力されます。Apache 互換の web サーバに配置された場合, このページは次の URL でアクセスできます:

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

### Opt-out

自動的に名前を変更したくないページがある場合, 除外できます:

``` ruby
page "/i-really-want-the-extension.html", :directory_index => false
```

1 度に沢山のファイルのインデックスをオフにしたい場合は, `page` には正規表現かファイルの塊を与えることができます。

ページ毎に [YAML 形式の Frontmatter](/frontmatter/) に `directory_index: false` を追加することもできます。

### 手動インデックス

テンプレートファイル名がすでに `index.html` になっている場合, この処理行われず Middleman を通過します。例えば, あなたが予想するように `my-page/index.html.erb` は `my-page/index.html` を生成します。

[rack-contrib]: https://github.com/rack/rack-contrib/
