---
title: ブログ機能
---

# ブログ機能

Middleman にはブログ, 記事とタグ付けに対応した公式拡張があります。 `middleman-blog` は拡張の 1 つで使用するにはインストールする必要があります。簡単に `Gemfile` で gem を指定します:

``` ruby
gem "middleman-blog"
```

もしくは, Bundler を使わずに手動でインストールします:

``` bash
gem install middleman-blog
```

次に, `config.rb` で拡張を有効化します:

``` ruby
activate :blog do |blog|
  # ブログ機能のオプションを設定
end
```

また, ブログ機能が用意された新しいプロジェクトを作成できます:

``` bash
middleman init MY_BLOG_PROJECT --template=blog
```

すでに Middleman のプロジェクトがある場合, サンプルの [`index.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/index.html.erb), [`tag.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/tag.html.erb), [`calendar.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/calendar.html.erb) や [`feed.xml`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/feed.xml.builder) を作成するために, blog テンプレートオプションとともに `middleman init` を再実行するか, 自分で作成してください。[何が作成されるのか](https://github.com/middleman/middleman-blog/tree/master/lib/middleman-blog/template/source) は GitHub で確認できます。

サイトマップマニュピレータは順序に依存する傾向があるので, [`ディレクトリインデックス`](/pretty-urls/) を使用する場合, ブログ拡張を有効化した *後* にディレクトリインデックスを有効化するべきです。例えば:

``` ruby
activate :blog do |blog|
  # set options on blog
end

activate :directory_indexes
```

## 記事

Middleman 自体がそうであるように, ブログ拡張は個々のファイルに焦点を当てています。それぞれの記事は, あなたが好きなテンプレート言語を使用しています。記事のデフォルトのファイル名構造は `:year-:month-:day-:title.html` です。新しい記事を作成したい場合, 正しいパスに配置し, 動作させるための基本的な [frontmatter](/frontmatter/) を含めます。`config.rb` で `:blog` が有効化されている場合, どの形式で Middleman が記事を探すのか変更する `blog.source` オプションを設定できます。

Middleman に関する新しい投稿を作成するとしましょう。`source/2011-10-18-middleman.html.markdown` を作成します。このファイルの最小限の内容は frontmatter に `title` を入力したものです:

``` html
--- 
title: Middleman ブログの投稿
---

こんにちわ世界
```

必要な場合, 同日の複数投稿をに対応するために, frontmatter に `date` 投稿として完全な日付と時刻を指定することができます。タグページを作成するために frontmatter に `tags` リストを含めることもできます。

ショートカットとして, `middleman article TITLE` を実行することで, Middleman は新しい投稿を正しい場所, 正しいファイル名で作成します。

### カスタムパス

ブログのデフォルトのパスは `/` (web サイトのルート) ですが, `config.rb` で上書きできます:

``` ruby
activate :blog do |blog|
  blog.prefix = "blog"
end
```

他のすべての設定 (`permalink`, `tag_path` など) に `prefix` が追加されるので, すべての設定でこれを繰り返す必要はありません。

投稿を閲覧するためのパーマリンクは次のように簡単に変更できます:

``` ruby
activate :blog do |blog|
  blog.permalink = "blog/:year/:title.html"
end
```

これで, あなたの記事は次の URL で閲覧できます: `blog/2011/blog.html` 。パーマリンクは投稿が保管されている形式と完全に異なっても構いません。デフォルトでは, パーマリンクのパスは `:year-:month-:day-:title.html` です。ブログ記事を HTML ファイルとしてではなくディレクトリとして表示したい場合, [きれいな URL](/pretty-urls/) 機能を有効化することもできます。

### 下書き

frontmatter に下書きの目印をつけられます:

``` html
---
title: 作業中
published: false
---

完了していない下書き
```

未発表の記事は開発モードでのみ表示されます。

未来の日付の記事も未発表とみなされます; 定期的に `cron` ジョブを使ってサイトを生成する場合, 自動的に指定された時間に記事を公開するために使用できます。

### タイムゾーン

RSS フィードで正確な公開時刻の取得や, 自動的に正確なスケジュールで記事を公開するために, ブログのタイムゾーンを `config.rb` で設定できます:

``` ruby
Time.zone = "Tokyo"
```

## 要約

デフォルトでは, パーマリンクページの外から見た場合, 記事の内容は途中で切り捨てられます。ブログ機能の拡張は記事の中の `READMORE` を探し, ホームページにこの前までのテキストの内容のみ表示します。パーマリンクページではこのメタデータを除去します。

これは `config.rb` で変更できます:
This can be changed in `config.rb`:

``` ruby
activate :blog do |blog|
  blog.summary_separator = /SPLIT_SUMMARY_BEFORE_THIS/
end
```

[`BlogArticle`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle) の [`summary`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle#summary-instance_method) 属性からテンプレートの中で要約を使用できます。

`summary` は要約を切り出す長さやテキストが切り捨てられた際に使用する文字列を取得するメソッドです:

```erb
<%= article.summary(250, '>>') =>
```

250文字以下の文字と続く ">>" が生成されます:

HTML 対応の要約の提供には, 要約を使用するために `gem 'nokogiri'` を `Gemfile` に追加しなければならないことに注意してください。

もし要約を生成する独自のメソッドがある場合, `blog.summary_generator` に `Proc` をセットできます。レンダリングされたブログの記事、希望の長さ、および省略文字列を受け取り、要約を生成します。

## タグ

タグを使用した記事の整理なしに何がブログ機能でしょうか? 記事の [frontmatter](/frontmatter/) に `tag` を追加するだけです。[`BlogArticle`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle) の [`tag`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle#tags-instance_method) メソッドを使うことでタグを呼び出すことができます。さらに, [`blog.tags`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogData#tags-instance_method) から記事に関連するタグリストを取得できます。`config.rb` で `blog.tag_template` にテンプレートを設定した場合 ([デフォルトの config.rb](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/config.tt) 参照) , タグ毎にページをレンダリングできます。タグテンプレートはローカル変数を持ちます。現在のタグがセットされた `tagname` とそのタグの記事リストがセットされた `article`です。また, 特定のタグページへのリンクを作成するために [`tag_path`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers#tag_path-instance_method) ヘルパを使用することができます。

デフォルトのテンプレートは `tag/TAGNAME.html` でタグ毎に [`tag.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/tag.html.erb) テンプレートを作成します。上記の例にいくつかのタグを追加すると次にようになります:

``` html
--- 
title: Middleman のブログ投稿
date: 2011/10/18
tags: blogging, middleman, hello, world
---

こんにちわ世界
```

これで `tags/blogging.html` で記事一覧を確認できます。

パスは `config.rb` で変更できます:

``` ruby
activate :blog do |blog|
  blog.taglink = "categories/:tag.html"
end
```

これで `categories/blogging.html` で記事一覧を確認できます。

## カレンダーページ

多くのブログエンジンは年月日ごとの全記事を載せたページを作成します。Middleman は [`calendar.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/calendar.html.erb) テンプレートと `blog.calendar_template` 設定を使用してこれを実現します。デフォルトのテンプレートは [`calendar.html`](https://github.com/middleman/middleman-blog/blob/master/lib/middleman-blog/template/source/calendar.html.erb) を作成します。このテンプレートは `year`, `month` と `day` 変数が設定され, その日付の記事一覧を作成します。

特定のカレンダーのページにしたい場合 (例えば年別で日別は不要) やカレンダーページの種類毎に異なるテンプレートを使用したい場合, `blog.year_template`, `blog.month_template` や `blog.day_template` を個別に設定できます。`blog.calendar_template` の設定はこれらすべてを設定するショートカットです。

テンプレートで, カレンダーページへのリンクを作成するために, [`blog_year_path`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers#blog_year_path-instance_method), [`blog_month_path`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers#blog_month_path-instance_method) や [`blog_day_path`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers#blog_day_path-instance_method) ヘルパを使用できます。`blog.year_link`, `blog.month_link` や `blog.day_link` の設定でこれらのリンクがどう見えるのかカスタマイズできます。デフォルトでは, カレンダーページは年月日毎に `/2012.html`, `/2012/03.html` や `/2012/03/15.html` のように表示されます。

## ページネーション

長い記事一覧は複数ページに分割できます。テンプレートはページ内で frontmatter に次の設定がされていると分割されます。

``` html
---
pageable: true
---
```

またページネーションは `config.rb` の中で有効化されます:

``` ruby
activate :blog do |blog|
  blog.paginate = true
end
```
デフォルトでは 2 ページ目以降は `/2012/page/2.html` のようなリンクになります; これはページあたりの記事数とともに `config.rb` でカスタマイズ可能です。例えば:

``` ruby
activate :blog do |blog|
  blog.paginate = true
  blog.page_link = "p:num"
  blog.per_page = 20
end
```

上記の設定は, ページあたり 20 記事でリンクは `/2012/p2.html` のような結果になります。`per_page` パラメータはテンプレートの frontmatter でテンプレート毎に設定できます。

ページネーション対応のテンプレートでは次の変数が使用できます:

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
               # article.length と使用し "Articles X to Y of Z" を表示"

next_page      # シーケンス内の次と前のページのためのリソース
prev_page      # 隣接するページが存在しない場合 nil
               # このページと他のページすべてを含む
```

`paginate` が false で `per_page` がテンプレートの frontmatter に設定されている場合, `page_articles` 変数は `article` の最初の `per_page` 分のアイテムが設定されます。これが有効になっていると改ページせずに使用できるテンプレートを作成できます。

## レイアウト

`config.rb` ですべての記事に使用される特定の [レイアウト](/templates/#toc_3) を設定できます:

``` ruby
activate :blog do |blog|
  blog.layout = "blog_layout"
end
```

レイアウトに挿入する前に記事毎にちょっとした構造でラップしたい場合, 記事レイアウトを作成してからメインのレイアウトでラップするために, Middleman の [入れ子レイアウト](/templates/#toc_4) 機能を使用できます。

## 記事データ

ブログの記事一覧はテンプレートから [`BlogArticle`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle) のリストを返す [`blog.articles`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogData#articles-instance_method) でアクセスできます。

各 [`BlogArticle`](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/BlogArticle) は有益なメソッドを持ち, [サイトマップ](/advanced/sitemap) からより情報 ([frontmatter](/frontmatter/) から [`data`](http://rubydoc.info/github/middleman/middleman/master/Middleman/Sitemap/Resource#data-instance_method) のような) が詰まった [`Resource`](http://rubydoc.info/github/middleman/middleman/master/Middleman/Sitemap/Resource) を作成することもできます。

例えば, 次のブロックは直近 5 記事とそれらの要約を示します:

``` html
<% blog.articles[0...5].each do |article| %>
  <article>
    <h1>
      <a href="<%= article.url %>"><%= article.title %></a>
      <time><%= article.date.strftime('%b %e %Y') %></time>
    </h1>

    <%= article.summary %>

    <a href="<%= article.url %>">もっと読む</a></div>
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

カレンダー一覧も同じように:

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

ブログ記事に従って拡張子なしのサブディレクトリは, ビルド時に正しい場所へ複製されたファイルが入っています。例えば, 次のディレクトリ構造です:

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

単一のブログ記事に属しているファイル (例えば画像) は source 内で一緒に保管し出力することができます。ブログ構造に依存して, 記事の中で相対リンクの使用を可能にしますが, 記事の内容がサイトの他の部分, 例えばカレンダーやタグページ, で使用される場合には注意が必要です。

## ヘルパ

テンプレートを簡単に作成するために使用できる [いくつかのヘルパ](http://rubydoc.info/github/middleman/middleman-blog/master/Middleman/Blog/Helpers) があります。これらは現在ページの記事を取得, 現在ページをブログ記事か判定, タグやカレンダーページへのパスを作成といったことができます。
