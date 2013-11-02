---
title: ブログ機能
---

# ブログ機能

Middleman にはブログ, つまり記事投稿とタグ付けに対応した公式拡張があります。 `middleman-blog` は拡張機能の 1 つで使うにはインストールする必要があります。簡単に `Gemfile` で gem を指定するだけです:

``` ruby
gem "middleman-blog"
```

もしくは Bundler を使わずに手動でインストールします:

``` bash
gem install middleman-blog
```

そして `config.rb` で拡張機能を有効化します:

``` ruby
activate :blog do |blog|
  # ブログ機能のオプションを設定
end
```

さらに, ブログ機能が用意された新しいプロジェクトを作ることができます:

``` bash
middleman init MY_BLOG_PROJECT --template=blog
```

すでに Middleman のプロジェクトがある場合, サンプルの [`index.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/index.html.erb), [`tag.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/tag.html.erb), [`calendar.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/calendar.html.erb) や [`feed.xml`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/feed.xml.builder) を作るために, blog テンプレートオプションとともに `middleman init` を再実行するか, 自分で作ってください。[何が作られるのか](https://github.com/middleman/middleman-blog/tree/master/lib/middleman-blog/template/source) は GitHub で確認できます。

サイトマップの動作は順序に依存する傾向があるので, [`ディレクトリインデックス`](/pretty-urls/) を使う場合, ブログ拡張を有効化した *後* にディレクトリインデックスを有効化するべきです。例えば:

``` ruby
activate :blog do |blog|
  # ブログ機能のオプションを設定
end

activate :directory_indexes
```

## 記事

Middleman 自体がそうであるように, ブログ拡張は個別ファイルにフォーカスしています。それぞれの記事はファイルごとに好きなテンプレート言語を使用できます。記事のデフォルトのファイル名構造は `:year-:month-:day-:title.html` です。新しい記事を作りたい場合, 正しいパスに配置し, 動作させるために基本的な [frontmatter](/frontmatter/) を記述します。`config.rb` で `:blog` が有効化されている場合, どのファイル形式で Middleman が記事を探すのか変更する `blog.source` オプションを設定できます。

Middleman に関する新しい投稿を作るとしましょう。`source/2011-10-18-middleman.html.markdown` を作ります。このファイルの最小限の内容は frontmatter に `title` を入力したものです:

``` html
--- 
title: Middleman ブログの投稿
---

Hello World
```

必要な場合, 同日の複数投稿に対応するために, frontmatter に `date` として日付と時刻を指定することができます。タグページを作るために frontmatter に `tags` リストを含めることもできます。

ショートカットとして, `middleman article TITLE` を実行することで Middleman は新しい投稿を正しい場所, 正しいファイル名で作ります。

### カスタムパス

ブログのデフォルトのパスは `/` (Web サイトのルート) ですが, `config.rb` で上書きできます:

``` ruby
activate :blog do |blog|
  blog.prefix = "blog"
end
```

他のあらゆる設定 (`permalink`, `tag_path` など) に `prefix` が追加されるので, 他の設定でこれを繰り返す必要はありません。

### パーマリンクのカスタマイズ

投稿を閲覧するためのパーマリンクは次のように簡単に変更できます:

``` ruby
activate :blog do |blog|
  blog.permalink = "blog/:year/:title.html"
end
```

これであなたの記事は次の URL で閲覧できます: `blog/2011/blog.html`。 パーマリンクは投稿が保存されているディレクトリからは完全に独立しています。デフォルトのパーマリンクのパスは `:year/:month/:day/:title.html` です。 パーマリンクは記事の日付要素 (:year, :month, :date), 記事タイトル, その他記事の中で使用されている frontmatter に定義されたデータ によって構成することができます。

例えば, 記事の frontmatter に category を定義しパーマリンクに含みたい場合:

```html
---
title: Middleman ブログ投稿
date: 2013/10/13
category: HTML5
---

Hello World
```

``` ruby
activate :blog do |blog|
  blog.permalink = "blog/:category/:title.html"
end
```

上記記事の URL は `blog/html5/my-middleman-blog-post.html` になります。

ブログ投稿を HTML ファイルではなくディレクトリとして表示したい場合には [きれいな URL](/pretty-urls/) 機能を有効化することもできます。

### 下書き

frontmatter に下書きの目印をつけられます:

``` html
---
title: 作業中
published: false
---

完了していない下書き
```

下書き記事は開発モードでのみ表示されます。

未来の日付の記事も未発表とみなされます; 定期的に `cron` ジョブを使ってサイトを生成する場合, 自動的に指定された時間に記事を公開するためにこの機能を使うことができます。

### タイムゾーン

RSS フィードで正確な公開時刻の取得や, 自動で正確なスケジュールで記事を公開するために, ブログのタイムゾーンを `config.rb` で設定できます:

``` ruby
Time.zone = "Tokyo"
```

## 要約

ホームページ上のようにリンクをともなった記事の要約を表示したい場合, Middleman は記事の切り取りに対応します。ブログ拡張は記事の中から `READMORE` を探し, ホームページ上にはこの文字列前までの内容を表示します。記事ページではこの情報は取り除かれます。

ブログ拡張が検索し切り取るテキストは `config.rb` で設定できます:

``` ruby
activate :blog do |blog|
  blog.summary_separator = /SPLIT_SUMMARY_BEFORE_THIS/
end
```

次に, ホームページ上のテンプレート (または要約を表示したい場所) に次の行を追加することで記事ページヘのリンクをともなった要約を表示することができます。

``` erb
<%= article.summary =>
<%= link_to 'Read more…', article >
```

_(注意することとして, デフォルトのレイアウトを使っている場合,  `<%= article.body =>` の行を置き換えます。)_

`READMORE` (または設定したテキスト) が削除された状態で記事へリンクします。

[`BlogArticle`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle) の [`summary`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle#summary-instance_method) 属性からテンプレートの中で要約を使うことができます。

`summary` は要約を切り出す長さやテキストが切り捨てられた際に表示する文字列を与えられるメソッドです:

```erb
<%= article.summary(250, '>>') %>
```

250文字以下の要約と続く ">>" が出力されます:

HTML 対応の要約の提供には, 要約を使うために `gem 'nokogiri'` を `Gemfile` に追加しなければならないので注意してください。

もし要約を生成する独自のメソッドがある場合, `blog.summary_generator` に `Proc` をセットできます。レンダリングされたブログの記事、切り取りたい長さ、および省略文字列を受け取り、要約を生成します。

## タグ

タグを使った記事の整理なしに何がブログ機能でしょうか? 単に記事の [frontmatter](/frontmatter/) に `tag` を追加するだけです。[`BlogArticle`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle) の [`tag`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle#tags-instance_method) メソッドを使うことでタグを呼び出すことができます。さらに, [`blog.tags`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogData#tags-instance_method) から記事に関連するタグリストを取得できます。`config.rb` で `blog.tag_template` にテンプレートを設定した場合 ([デフォルトの config.rb](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/config.tt) 参照) , タグごとにページをレンダリングできます。タグテンプレートはローカル変数を持ちます。現在のタグがセットされた `tagname` とそのタグの記事リストがセットされた `articles`です。また特定のタグページへのリンクを作るために [`tag_path`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers#tag_path-instance_method) ヘルパを使うことができます。

デフォルトのテンプレートでは `tag/TAGNAME.html` 形式でタグごとに [`tag.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/tag.html.erb) ページを作ります。上記の例にいくつかのタグを追加すると次にようになります:

``` html
--- 
title: Middleman のブログ投稿
date: 2011/10/18
tags: blogging, middleman, hello, world
---

Hello World
```

これで `tags/blogging.html` で表示された記事を確認できます。

パスは `config.rb` で変更できます:

``` ruby
activate :blog do |blog|
  blog.taglink = "categories/:tag.html"
end
```

これで `categories/blogging.html` で記事一覧を確認できます。

## カレンダーページ

多くのブログエンジンは年月日ごとの全記事を載せたページを作ります。Middleman は [`calendar.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/calendar.html.erb) テンプレートと `blog.calendar_template` 設定を使ってこれを実現します。デフォルトのテンプレートは [`calendar.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/calendar.html.erb) を作ります。このテンプレートでは `year`, `month` と `day` 変数が設定され, その日付の記事一覧を出力します。

特定形式のカレンダーページにしたい場合 (例えば年別で日別は不要) やカレンダーページの種類ごとに異なるテンプレートを使いたい場合, `blog.year_template`, `blog.month_template` や `blog.day_template` を個別に設定できます。`blog.calendar_template` の設定はこれらすべてを設定するショートカットです。

テンプレート内で, カレンダーページへのリンクを作るために, [`blog_year_path`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers#blog_year_path-instance_method), [`blog_month_path`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers#blog_month_path-instance_method) や [`blog_day_path`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers#blog_day_path-instance_method) ヘルパを使うことができます。`blog.year_link`, `blog.month_link` や `blog.day_link` の設定でこれらのリンクがどう表示されるのかカスタマイズできます。デフォルトでは, カレンダーページは年月日ごとに `/2012.html`, `/2012/03.html` や `/2012/03/15.html` のように表示されます。

## カスタム記事コレクション

Middleman ブログは [frontmatter](/frontmatter/) に定義したデータによって記事をグループ分けする機能に対応しています。次の一般的な例では *category* 属性を使って記事をグループ分けします。

```html
---
title: Middleman ブログの投稿
date: 2013/10/13
category: HTML5
---

Hello World
```

HTML5 のカテゴリに属するすべての記事を表示する `categories/html5.html` を生成するように Middleman ブログを設定できます。次の設定例を参照してください:

```ruby
activate :blog do |blog|
  blog.custom_collections = {
    :category => {
      :link => '/categories/:category.html',
      :template => '/category.html'
    }
  }
end
```

category 属性に基づいてコレクションを設定します。ビルドする際のカスタムページの URL 構造や使用するテンプレートを指定することができます。カスタムコレクションを生成する場合, コレクションページにアクセスする新しいヘルパが生成されます。

### カスタムコレクションヘルパ

上記の例ではヘルパメソッドは `category_path` という名前で生成されます。これは `category_path('html5')` で呼び出すことができ `categories/html5.html` という URL を返します。

## ページネーション

長い記事の一覧は複数ページに分割できます。テンプレートは次の設定がされているとページ分割されます。

``` html
---
pageable: true
---
```

frontmatter で有効化に加え, ページネーションは `config.rb` の中で有効化できます:

``` ruby
activate :blog do |blog|
  blog.paginate = true
end
```
デフォルト設定では 2 ページ目以降は `/2012/page/2.html` のようなリンクになります; これはページあたりの記事数とともに `config.rb` でカスタマイズ可能です。例えば:

``` ruby
activate :blog do |blog|
  blog.paginate = true
  blog.page_link = "p:num"
  blog.per_page = 20
end
```

上記の設定は, ページあたり 20 記事でリンクは `/2012/p2.html` のような結果になります。`per_page` パラメータはテンプレートの frontmatter でテンプレートごとに設定できます。

ページネーション対応のテンプレートでは次の変数を使うことができます:

``` ruby
paginate       # サイトでページネーションを有効化する場合には true を設定
per_page       # ページあたりの記事数

page_articles  # 記事一覧をこのページで表示する
articles       # テンプレートのための完全な記事一覧

page_number    # ページ番号
num_pages      # 総ページ数。次のように page_numer と一緒に
               # "Page X of Y" を表示する

page_start     # このページの最初の記事の番号
page_end       # このページの最後の記事の番号
               # article.length を使って "Articles X to Y of Z" を表示"

next_page      # シーケンス内の次と前のページのためのリソース
prev_page      # 隣接するページが存在しない場合 nil
               # このページと他のページすべてを含む
```

`paginate` が false で `per_page` がテンプレートの frontmatter に設定されている場合, `page_articles` 変数は `article` の最初の `per_page` 分のアイテムが設定されます。これが有効になっていると改ページせずに使用できるテンプレートを作ることができます。

## レイアウト

`config.rb` であらゆる記事に使われる特別な [レイアウト](/templates/#toc_3) を設定できます:

``` ruby
activate :blog do |blog|
  blog.layout = "blog_layout"
end
```

レイアウトに挿入する前に記事ごとにちょっとした構造で囲みたい場合, 記事レイアウトを構成してからメインのレイアウトで囲むために, Middleman の [入れ子レイアウト](/templates/#toc_4) 機能を使うことができます。

## 記事データ

ブログ記事の一覧はテンプレートから [`BlogArticle`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle) のリストを返す [`blog.articles`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogData#articles-instance_method) でアクセスできます。

各 [`BlogArticle`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle) は有益なメソッドを持ちます。[サイトマップ](/advanced/sitemap) からより情報 ([frontmatter](/frontmatter/) から [`data`](http://rubydoc.info/github/middleman/middleman/Middleman/Sitemap/Resource#data-instance_method) のような) が詰まった [`Resource`](http://rubydoc.info/gems/middleman-core/Middleman/CoreExtensions/FrontMatter/ResourceInstanceMethods#data-instance_method) を得ることもできます。

例えば, 次のブロックは最新 5 記事とそれらの要約を示します:

``` html
<% blog.articles[0...5].each do |article| %>
  <article>
    <h1>
      <a href="<%= article.url %>"><%= article.title %></a>
      <time><%= article.date.strftime('%b %e %Y') %></time>
    </h1>

    <%= article.summary %>

    <a href="<%= article.url %>">もっと読む</a>
  </article>
<% end %>
```

タグアーカイブ用のタグデータへのアクセスを得ることもできます:

``` html
<ul>
  <% blog.tags.each do |tag, articles| %>
    <li>
      <h5><%= tag %></h5>
      <ul>
        <% articles.each do |article| %>
          <li><a href="<%= article.url %>"><%= article.title %></a></li>
        <% end %>
      </ul>
  <% end %>
</ul>
```

さらにカレンダー一覧も同じように:

``` html
<ul>
  <% blog.articles.group_by {|a| a.date.year }.each do |year, articles| %>
    <li>
      <h5><%= year %></h5>
      <ul>
        <% articles.each do |article| %>
          <li><a href="<%= article.url %>"><%= article.title %></a></li>
        <% end %>
      </ul>
    </li>
  <% end %>
</ul>
```

`public` フラグを frontmatter に追加した場合:

``` html
<h1>公開記事</h1>
<% blog.articles.select {|a| a.page.data[:public] }.each do |article| %>
  ...
<% end %>
```

## 記事のサブディレクトリ

ブログ記事に紐付いた拡張子なしのサブディレクトリは, ビルド時に正しい場所へ複製されたファイルが入っています。例えば, 次のディレクトリ構造です:

```
source/2011-10-18-middleman.html.markdown
source/2011-10-18-middleman/photo.jpg
source/2011-10-18-middleman/source_code.rb
```

この出力 ([`directory_indexes`](/pretty-urls/) が有効化された場合) は次のようになります:

```
build/2011/10/18/middleman/index.html
build/2011/10/18/middleman/photo.jpg
build/2011/10/18/middleman/source_code.rb
```

単一のブログ記事に属しているファイル (例えば画像) は source 内で一緒に保管し出力することができます。ブログ構造に依存し, 記事の中で相対リンクの使用を可能にしますが, 記事の内容がサイトの他の部分, 例えばカレンダーやタグページ, で使われる場合には注意が必要です。

## ヘルパ

テンプレートを簡単に作るために使用できる [いくつかのヘルパ](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers) があります。これらは現在ページの記事を取得, 現在ページをブログ記事か判定, タグやカレンダーページへのパスを作成といったことができます。
