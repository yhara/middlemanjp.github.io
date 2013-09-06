---
title: テンプレート
---

# テンプレート

Middleman は HTML の開発を単純化するために多くのテンプレート言語を提供します。その言語はページ内で変数やループを使えるようにするものから HTML にコンパイルされたページを書くために全く異なった形式を提供するものにまで及びます。 Middleman は ERb, Haml, Sass, Scss や CoffeeScript のサポートを搭載しています。Tilt が有効な gem であればその他にも多くのエンジンが有効化できます。[次のリストを参照してください](#toc_7)。

## テンプレートの基礎

デフォルトのテンプレート言語は ERb です。ERb は変数の追加, メソッド呼び出し, ループの使用や if 文を除き, そのままの HTML です。このガイドの次のセクションでは使用例として ERb を使用します。

Middleman で使うすべてのテンプレートはそのファイル名にテンプレート言語の拡張子を含みます。ERb で書かれたシンプルな index ページは 完全なファイル名の `index.html` と ERb の拡張子を含む `index.html.erb` という名前になります。

まず, このファイルは標準の HTML が書かれています:

``` html
<h1>ようこそ</h1>
```

私たちは思いつきでループを追加することができます:

``` html
<h1>ようこそ</h1>
<ul>
  <% 5.times do |num| %>
    <li>カウント <%= num %>
  <% end %>
</ul>
```

## レイアウト

レイアウトはすべてのテンプレート間で共有する, 個別ページを囲むための共通の HTML の使用を可能にします。PHP 開発経験のある開発者はページ毎に, その上部と下部に "header" や "footer" への参照をもつ使い方を使用したことがあるでしょう。Ruby と Middleman では逆のアプローチを取ります。"layout" は "header" や "footer" 両方を含み個別ページのコンテンツを囲みます。

最も基本的なレイアウトはいくつかの共有コンテンツとそのテンプレートの内容を配置する `yield` があります。

 ERb を使ったレイアウトの例です:

``` html
<html>
<head>
  <title>私のサイト</title>
</head>
<body>
  <%= yield %>
</body>
</html> 
```

ERb で書かれたページテンプレートが与えられます:

``` html
<h1>こんにちわ世界</h1>
```

組み合わされた最終的な HTML 出力は次のように:

``` html
<html>
<head>
  <title>私のサイト</title>
</head>
<body>
  <h1>こんにちわ世界</h1>
</body>
</html>
```

ファイルの拡張子とパーサに関しては, レイアウトはビルドプロセスの中でテンプレートと異なる機能をもっているので, 正しい拡張子を与えるよう注意する必要があります。次がその理由です:

断片をテンプレートに集めるような場合, ファイル拡張子は重要です。例えば, レイアウトファイルを `layout.html.erb` と名付けることで, 言語パーサにこのファイルは erb として扱えと命じることになり, html に変換されます。

ある意味で, 拡張子を右から左に読むことは, ファイルが左端の拡張子形式のファイルとしてパース処理されることを知らせます。例の場合, ファイルが与えられた時に erb から html に変換し, ファイルをビルドします。

テンプレートとは異なり, レイアウトは html にレンダリングされるべきではありません。レイアウトのファイル名の左端の拡張子に `.html` を与えた場合, ビルド時のエラーの原因になります。したがって, 例えば `layout.erb` のように, 拡張子をつける必要があります。

### カスタムレイアウト

デフォルトでは, Middleman はサイト内のすべてのページに同じレイアウトを使用します。しかし, 複数のレイアウトを使用し, どのページがその他のレイアウトを使用するのか指定したい場合があります。例えば, それぞれ独自のレイアウトをもつ "public" サイトと "admin" サイトがあるような場合です。

デフォルトのレイアウトは `source` フォルダの中で "layout" と名付けられ, 使用するテンプレート言語の拡張子を持ちます。デフォルトでは `layout.erb` です。あなたが作成するその他のレイアウトは `source/layouts` フォルダに配置されます。

admin 用の新しいレイアウトを作成するには, `source/layouts` フォルダに新たに "admin.erb" ファイルを追加します。次の内容だとしましょう:

``` html
    <html>
    <head>
      <title>管理エリア</title>
    </head>
    <body>
      <%= yield %>
    </body>
    </html>
```

次に, どのページがこのレイアウトを使用するのか指定する必要があります。次の 2 つの方法でこれを行うことができます。ページの大きなグループにこのレイアウトを適用したい場合, `config.rb` に "page" コマンドを使うことができます。 `source` フォルダの中に "admin" というフォルダがあり "admin" の中のテンプレートは admin レイアウトを使用するとしましょう。 `config.rb` は次のようになります:

``` ruby
page "/admin/*", :layout => "admin"
```

ページのパスにワイルドカードを使用することで admin フォルダ以下のすべてのページが admin レイアウトを使用するように指定しています。

ページを直接参照することもできます。例えば, source フォルダに `login.html.erb` が配置されているが, admin レイアウトを適用したい場合です。ページテンプレートの例として次を使いましょう。

``` html
<h1>Login</h1>
<form>
  <input type="text" placeholder="Email">
  <input type="password">
  <input type="submit">
</form>
```

この特殊なページに次のようにカスタムレイアウトを指定できます:

``` ruby
page "/login.html", :layout => "admin"
```

これは login ページが admin レイアウトを使用するように指定しています。 `config.rb` ですべて指定する代わりに, [Frontmatter] を使ってテンプレートのページ毎にレイアウトを指定することもできます。`login.html.erb` ページ自身にレイアウトを指定する例です。

``` html
---
layout: admin
---

<h1>Login</h1>
<form>
  <input type="text" placeholder="Email">
  <input type="password">
  <input type="submit">
</form>
```

### 入れ子レイアウト

入れ子レイアウトはレイアウトの積み重ねを作成できます。これを理解する最も簡単なユースケースは `middleman-blog` 拡張です。ブログ記事はサイト全体のコンテンツの部分集合です。追加された内容と構造を含みますが, 最終的にサイト全体の構造によってラップされる必要があります (header, footer など) 。

シンプルなデフォルトのレイアウトは次のようになります:

``` html
<html>
  <body>
    <header>ヘッダ</header>
    <%= yield %>
    <footer>フッタ</footer>
  </body>
</html>
```

blog 記事が `blog/my-article.html.markdown` に配置されているとします。すべての blog 記事は デフォルトの `layout` に代わって `article_layout` を使用するように設定します。 `config.rb` の設定です:

``` ruby
page "blog/*", :layout => :article_layout
```

`layouts/article_layout.erb` は次のようになります:

``` html
<% wrap_layout :layout do %>
  <article>
    <%= yield %>
  </article>
<% end %>
```

通常のレイアウトと同じように, `yield` はテンプレートの出力内容が配置される場所です。この例では次の出力で終わります。

``` html
<html>
  <body>
    <header>ヘッダ</header>
    <article>
      <!-- テンプレート/ブログ記事の内容 -->
    </article>
    <footer>フッタ</footer>
  </body>
</html>
```

### 完全なレイアウト無効化

いくつかの場合, まったくレイアウトを使用したくない場合があります。 `config.rb` でデフォルトのレイアウトを無効化することで対応できます。

``` ruby
set :layout, false

# Or for an individual file:
page '/foo.html', :layout => false
```

## パーシャル

パーシャルは重複を避けるためにページ全体にわたってコンテンツを共有する方法です。パーシャルはページテンプレートとレイアウトで使用されます。上記 2 つの例を続けましょう: 通常のページと admin ページです。この 2 つのレイアウトには footer のような重複する内容があります。 footer パーシャルを作成し, これらのレイアウトで使用してみましょう。

パーシャルのファイル名は prefix にアンダースコアが付き, 使用するテンプレート言語の拡張子を含みます。例として `source` フォルダに配置される `_footer.erb` と名付けられた footer パーシャルを示します:

``` html
<footer>
  Copyright 2011
</footer>
```

次に, "partial" メソッドを使ってデフォルトのレイアウトにパーシャルを配置します:

``` html
<html>
<head>
  <title>私のサイト</title>
</head>
<body>
  <%= yield %>
  <%= partial "footer" %>
</body>
</html>
```

admin レイアウトでは次のように:

``` html
<html>
<head>
  <title>管理エリア</title>
</head>
<body>
  <%= yield %>
  <%= partial "footer" %>
</body>
</html>
```

すると, `_footer.erb` への変更は, このパーシャルを使用するそれぞれのレイアウトやレイアウトを使用するページに表示されます。

複数のページやレイアウトに Copy&Paste する内容を見つけた場合, パーシャルに内容を抽出するのは良い方法です。

パーシャルを使い始めたら, 変数を渡すことで異なった呼び出しを行いたいかもしれません。次の方法で対応出来ます:

``` html
<%= partial(:paypal_donate_button, :locals => { :amount => 1, :amount_text => "Pay $1" }) %>
<%= partial(:paypal_donate_button, :locals => { :amount => 2, :amount_text => "Pay $2" }) %>
```

すると, パーシャル内で, 次のようにテキストを設定することができます:

``` html
<form action="https://www.paypal.com/cgi-bin/webscr" method="post">
  <input name="amount" type="hidden" value="<%= "#{amount}.00" %>" >
  <input type="submit" value=<%= amount_text %> >
</form>
```

詳細については [Padrino partial helper] のドキュメントを読んでください。

## テンプレートエンジンオプション

`config.rb` にテンプレートエンジンのオプションを設定することができます:

```ruby
set :haml, { :ugly => true, :format => :html5 }
```

## Markdown

`config.rb` で一番好きな Markdown ライブラリを選び, オプションを設定することができます:

```ruby
set :markdown_engine, :redcarpet
set :markdown, :fenced_code_blocks => true, :smartypants => true
```

RedCarpet を使用する場合, Middleman はヘルパを用いて `:relative_links` や `:asset_hash` が行うようにリンクや画像タグを処理します。しかし, デフォルトの Markdown エンジンはインストールが容易なことから Kramdown です。


### 他のテンプレート言語

Tilt 対応のテンプレート言語と RubyGems のリストです。動作させるにはインストール (`config.rb` で読み込む) しなければなりません。

エンジン                | ファイル拡張子         | 必要なライブラリ
------------------------|------------------------|----------------------------
Slim                    | .slim                  | slim
Erubis                  | .erb, .rhtml, .erubis  | erubis
Less CSS                | .less                  | less
Builder                 | .builder               | builder
Liquid                  | .liquid                | liquid
RDiscount               | .markdown, .mkd, .md   | rdiscount
Redcarpet               | .markdown, .mkd, .md   | redcarpet
BlueCloth               | .markdown, .mkd, .md   | bluecloth
Kramdown                | .markdown, .mkd, .md   | kramdown
Maruku                  | .markdown, .mkd, .md   | maruku
RedCloth                | .textile               | redcloth
RDoc                    | .rdoc                  | rdoc
Radius                  | .radius                | radius
Markaby                 | .mab                   | markaby
Nokogiri                | .nokogiri              | nokogiri
CoffeeScript            | .coffee                | coffee-script
Creole (Wiki markup)    | .wiki, .creole         | creole
WikiCloth (Wiki markup) | .wiki, .mediawiki, .mw | wikicloth
Yajl                    | .yajl                  | yajl-ruby
Stylus                  | .styl                  | ruby-stylus

[Haml]: http://haml-lang.com/
[Slim]: http://slim-lang.com/
[Markdown]: http://daringfireball.net/projects/markdown/
[these guides are written in Markdown]: https://raw.github.com/middleman/middleman-guides/master/source/guides/basics-of-templates.html.markdown
[Frontmatter]: /frontmatter/
[Padrino partial helper]: http://www.padrinorb.com/api/classes/Padrino/Helpers/RenderHelpers.html
