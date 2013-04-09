---
title: サイトマップ
---

# サイトマップ

Middleman は, テンプレートからアクセスできるサイトマップを含み, サイト内のすべてのページとリソース, それらが互いにどのように関係するのか情報を持ちます。これはナビゲーションの作成, 検索ページやサイトマップの作成などに使用できます。

## サイトマップへのアクセス

[サイトマップ](http://rubydoc.info/github/middleman/middleman/master/Middleman/Sitemap/Store) はサイトのページ毎のリポジトリで, HTML, CSS, JavaScript, 画像, つまりはすべてのものを含みます。`:proxy` を使って作成した [動的ページ][dynamic pages] も含みます。テンプレート内で, `sitemap` はサイトマップオブジェクトから取得出来ます。そこから [`resources`](http://rubydoc.info/github/middleman/middleman/master/Middleman/Sitemap/Store#resources-instance_method) メソッドを介してすべてのページを調べるか, [`find_resource_by_path`](http://rubydoc.info/github/middleman/middleman/master/Middleman/Sitemap/Store#find_resource_by_path-instance_method) を介して個々のリソースを取得することができます。また, `current_resource` を介すことで, いつでも現在ページのページオブジェクトを取得もできます。サイトマップからページリストを取得すれば, 個々のページオブジェクトを使用することで様々なプロパティをフィルタすることができます。

## サイトマップページ

サイトマップ内の各リソースは [Resource](http://rubydoc.info/github/middleman/middleman/master/Middleman/Sitemap/Resource) オブジェクトです。ページはページ自身について興味深いものを教えてくれます。[frontmatter] データ, ファイル拡張子, ソースと出力パス, リンク可能な URL, MIME タイプなどにアクセスできます。ページプロパティの一部は Middleman の内部レンダリングにとても便利で, 例えばすべての `.html` ファイルを見つけ出すために, ファイル拡張子でページをフィルタリングする場合が想像できるでしょう。

それぞれのページはサイト階層内の関連する他のページを探すこともできます。 `parent`, `siblings` や `children` メソッドはナビゲーションメニューやパンくずリストを作成する際に特に便利です。

## config.rb 内のサイトマップの使用

`config.rb` から新しい [動的ページ][dynamic pages] を作成するためにサイトマップ情報を使用できます ([blog extension](/blogging/) がタグページを作成する方法です) が, サイトマップはすでに実行された *後の* `config.rb` まで読み込まれないので少し注意が必要です。これを回避するために, アプリケーションの `ready` イベントのコールバックを登録する必要があります。例として, ページの [frontmatter] に "category" 要素を追加し, カテゴリ毎に動的にカテゴリページを作成したいとします。 その場合 `config.rb` にこれを追加します:

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
