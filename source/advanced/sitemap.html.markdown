---
title: サイトマップ
---

# サイトマップ

Middleman は, テンプレートからアクセスできるサイトマップを含み, サイト内のすべてのページとリソース, それらが互いにどのように関係するのか情報を持ちます。これはナビゲーションの作成, 検索ページやフィードの作成などに使用できます。

[サイトマップ](http://rubydoc.info/github/middleman/middleman/Middleman/Sitemap/Store) はサイトのページ毎のリポジトリで, HTML, CSS, JavaScript, 画像, つまりはすべてのものを含みます。`:proxy` を使って作成した [動的ページ][dynamic pages] も含みます。

## サイトマップを見る

Middleman がサイトをどのように見ているのか正確に理解するために, プレビューサーバを起動し http://localhost:4567/__middleman/sitemap/ をブラウザで開きます。完全なサイトマップやソースへのパス, ビルド先のパス, URL などを確認できるでしょう。"path" には特に注意を払う必要があります: config.rb の中で `page`, `ignore` や `proxy` からファイルを参照するために使用します。

## コードからサイトマップにアクセス

`sitemap` テンプレート内でサイトマップオブジェクトを取得します。そこから, ページ毎に [`resources`](http://rubydoc.info/github/middleman/middleman/Middleman/Sitemap/Store#resources-instance_method) メソッドを経由して, または  [`find_resource_by_path`](http://rubydoc.info/github/middleman/middleman/Middleman/Sitemap/Store#find_resource_by_path-instance_method) を介して個別のリソースを見ることができます。いつでも `current_resource` を経由して現在ページのページオブジェクトを取得できます。サイトマップからページリストを取得できれば, 個々のページオブジェクトを使用して, 様々なプロパティをフィルタリングできます。

## サイトマップリソース

サイトマップ内の各リソースは, [Resource](http://rubydoc.info/github/middleman/middleman/Middleman/Sitemap/Resource) オブジェクトです。Resource オブジェクトはそれ自体の興味深いあらゆる種類のものを伝えます。[frontmatter] データへアクセス, ファイル拡張子, 出力パス, リンク URL などにアクセスできます。Resource オブジェクトの一部のプロパティは Middleman の内部レンダリングにとても便利です。例えば, すべての `.html` ファイルを見つけるためにファイル拡張子でページをフィルタリングすることが想像できます。

それぞれのページはサイト階層内の関連する他のページを探すこともできます。 `parent`, `siblings` や `children` メソッドはナビゲーションメニューやパンくずリストを作成する際に特に便利です。

サイトマップは ActiveRecord のような構文で照会できます:

```ruby
sitemap.where(:tags.include => "homepage").order_by(:priority).limit(10)
```

See [Middleman::Sitemap::Queryable](http://rubydoc.info/gems/middleman/middleman/Middleman/Sitemap/Queryable) for more on the query interface.

## config.rb 内のサイトマップの使用

サイトマップの情報を使用して `config.rb` から新しい [動的ページ][dynamic pages] の作成ができます。ただし, サイトマップは `config.rb` が実行された *後* まで用意されないので少し注意が必要です。これに対応するために, アプリケーションの `ready` イベントにコールバックを登録する必要があります。例として, ページの [frontmatter] に "category" 要素を追加したとしましょう。カテゴリー毎に動的にカテゴリーページを作成したいとします。`config.rb` に次の内容を追加:

``` ruby
ready do
  sitemap.resources.group_by {|p| p.data["category"] }.each do |category, pages|
    proxy "/categories/#{category}.html", "category.html", 
      :locals => { :category => category, :pages => pages }
  end
end
```

カテゴリリストを作成するために, `category` と `pages` 変数を使う `category.html.erb` を作成できます。

[dynamic pages]: /dynamic-pages/
[frontmatter]: /frontmatter/
