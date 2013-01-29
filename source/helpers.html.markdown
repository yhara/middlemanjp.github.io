---
title: テンプレートヘルパ
---

# テンプレートヘルパ

テンプレートヘルパは一般的な HTML の作業を簡単にするために, 動的テンプレートの中で使用できるメソッドです。基本的なメソッドのほとんどは Rails のビューヘルパを利用したことのある人にはお馴染みのものです。すべてのヘルパは Padrino フレームワークに組み込まれています。[完全なドキュメントはこちらを参照してください。][view the full documentation here]

## リンクヘルパ

Padrino はリンクタグを作るために使う `link_to` メソッドを提供します。最も基本的な使い方は, `link_to` が名称とリンク URL を引数に取ります:

``` html
<%= link_to '私のサイト', 'http://mysite.com' %>
```

`link_to` はより複雑な内容を提供できるように, ブロックをとることもできます:

``` html
<% link_to 'http://mysite.com' do %>
  <%= image_tag 'mylogo.png' %>
<% end %>
```

Middleman は `link_to` に [サイトマップ](/advanced/sitemap/) を知らせることで強化します。 source フォルダ (ファイル拡張子からテンプレート言語の拡張子を除いた状態で) のページを参照すると, `link_to` は [`:directory_indexes`](/pretty-urls/) の拡張が設定されていたとしても, 正しいリンクを生成します。例えば, `source/about.html` ファイルがあって `:directory_indexes` が設定されている場合, 次のようにリンクされます:

``` html
<%= link_to 'About', '/about.html' %>

結果: <a href='/about/'>About</a>
```

現在ページから相対パスで参照することもできます。一部の人はリンクを現在ページからの相対パスにしたいと考えます。 `:relative => true` を渡すことで, `link_to` は相対 URL になります。

`:directory_indexes` が設定され, source/foo/index.html.erb の中から相対パスを得る場合:

``` html
<%= link_to 'About', '/about.html', :relative => true %>
```

結果:

``` html
<a href='../about/'>About</a>
```

`link_to` で生成されるすべての URL を相対パスにしたい場合, `config.rb` に次を追加します:

``` ruby
set :relative_links, true
```

相対パスにしたくないリンクに `:relative => false` を追加することで個別に上書きできます。

## 出力ヘルパ

出力ヘルパは様々な方法で出力を管理, キャプチャ, 表示する重要なメソッドの集合で, より高いレベルのヘルパ機能をサポートするために使用されます。説明すべき出力ヘルパが 3 つあります: `content_for`, `capture_html` や `concat_content`。

`content_for` はコンテンツのキャプチャを行い, レイアウトの中など異なった場所でのレンダリングをサポートします。その 1 例はテンプレートからレイアウトに assets を含めるものです:

``` html
<% content_for :assets do %>
  <%= stylesheet_link_tag 'index', 'custom' %>
<% end %>
```

テンプレートに追加することで, ブロックに含まれる部分をキャプチャし, レイアウトで yield を用いて出力できます:

``` html
<head>
  <title>例</title>
  <%= stylesheet_link_tag 'style' %>
  <%= yield_content :assets %>
</head>
```

自動的にブロックの内容 (この場合はスタイルシートが含まれる) をレイアウト内で yield された場所に挿入します。

`content_for?` にキーを与えることで `content_for` ブロックが存在するか確認することもできます:

``` html
<% if content_for?(:assets) %>  
  <div><%= yield_content :assets %></div>
<% end %>
```

ブロックの引数も対応します。

``` ruby
yield_content :head, param1, param2
content_for(:head) { |param1, param2| ...content... }
```

## タグヘルパ

タグヘルパはビューテンプレート内の html "タグ" を作成するために使用される基本的なメソッドです。このカテゴリには 3 つの主要なメソッドがあります: `tag`, `content_tag` と `input_tag`。

`tag` と `content_tag` は名前と指定されたオプションで任意の html タグを作成するためのものです。 html タグが "中身" を含む場合, `content_tag` が使われます。例:

``` html
<%= tag :img, :src => "/my_image.png" %>
  # => <img src='/my_image.png'>

<%= content_tag :p, :class => "stuff" do %>
  こんにちわ
<% end %>
  # => <p class='stuff'>こんにちわ</p>
```

`input_tag` はユーザからの入力受付に関するタグを作成するために使われます:

``` ruby
input_tag :text, :class => "demo" 
  # => <input type='text' class='demo'>
input_tag :password, :value => "secret", :class => "demo"
  # => <input type='password' value='secret' class='demo'>
```

## アセットヘルパ

アセットヘルパはハイパーリンク, mail_to リンク, 画像, スタイルシートや JavaScript のような html をビューテンプレートに挿入する手助けをします。単純なビューテンプレートでの用途は次のようになります:

``` html
<html>
<head>
  <%= stylesheet_link_tag 'layout' %>
  <%= javascript_include_tag 'application' %>
  <%= favicon_tag 'images/favicon.png' %>
</head>
<body>
  <p><%= link_to 'Blog', '/blog', :class => 'example' %></p>
  <p>Mail me at <%= mail_to 'fake@faker.com', "Fake Email Link", 
                      :cc => "test@demo.com" %></p>
  <p><%= image_tag 'padrino.png', :width => '35', 
           :class => 'logo' %></p>
</body>
</html>
```

## Form ヘルパ

Form ヘルパは form を作成する際に使用するであろう "一般的な" form タグのヘルパです。非オブジェクトの form が作成される場合の例は次のように:

``` html
<% form_tag '/destroy', :class => 'destroy-form', :method => 'delete' do %>
  <% field_set_tag do %>
    <p>
      <%= label_tag :username, :class => 'first' %>
      <%= text_field_tag :username, :value => params[:username] %>
    </p>
    <p>
      <%= label_tag :password, :class => 'first' %>
      <%= password_field_tag :password, :value => params[:password] %>
    </p>
    <p>
      <%= label_tag :strategy %>
      <%= select_tag :strategy, :options => ['delete', 'destroy'],
          :selected => 'delete' %>
    </p>
    <p>
      <%= check_box_tag :confirm_delete %>
    </p>
  <% end %>
  <% field_set_tag(:class => 'buttons') do %>
    <%= submit_tag "削除" %>
  <% end %>
<% end %>
```

## フォーマットヘルパ

フォーマットヘルパはテキストを目的に合わせて成型するためのいくつかの便利なメソッドです。
フォーマットヘルパの代表的なものは `escape_html`, `distance_of_time_in_words`, `time_ago_in_words` と `js_escape_html` の 4 つです。

`escape_html` と `js_escape_html` メソッドは html 文字列を取得し, 特定の文字をエスケープするものです。
`escape_html` は HTML/XML のアンパサンド, 括弧や引用符をエスケープします。テンプレートに表示する前にユーザコンテンツをエスケープするのに便利です。 `js_escape_html` は JS テンプレートから JavaScript の関数に情報を与える場合に使用されます。

``` ruby
escape_html('<hello>&<goodbye>') # => &lt;hello&gt;&amp;&lt;goodbye&gt;
```

テンプレート内で簡単に使うために `h` という `escape_html` のエイリアスもあります。

フォーマットヘルパは `simple_format`, `pluralize`, `word_wrap` や `truncate` のようなテキストの成型に便利な機能も含みます。

``` ruby
simple_format("こんにちわ\nせかい")
  # => "<p>こんにちわ<br/>世界</p>"
pluralize(2, '人')
  # => '2 人'
word_wrap('Once upon a time', :line_width => 8) 
  # => "Once upon\na time"
truncate("Once upon a time in a world far far away", :length => 8) 
  # => "Once upon..."
truncate_words("Once upon a time in a world far far away", :length => 4)
  # => "Once upon a time..."
highlight('Lorem dolor sit', 'dolor') 
  # => "Lorem <strong class="highlight">dolor</strong> sit"
```

## ダミーテキスト & Placehold.it ヘルパ

[Frank プロジェクト][Frank project], Sinatra の影響も受けた静的ツールは, ランダムなテキストコンテンツとプレースホルダ画像を生成するための素晴らしいヘルパセットです。 私たちはこのコードを Middleman に適合させました (MIT ライセンスを祝福します)。

プレースホルダテキストの使用方法:

``` ruby
lorem.sentence      # 1 文を返す
lorem.words 5       # 5 つの単語を返す
lorem.word
lorem.paragraphs 10 # 10 段落を返す
lorem.paragraph
lorem.date          # strftime 形式の引数を受取る
lorem.name
lorem.first_name
lorem.last_name
lorem.email
```

プレースホルダ画像の使用方法:

``` ruby
lorem.image('300x400')
  #=> http://placehold.it/300x400
lorem.image('300x400', :background_color => '333', :color => 'fff')
  #=> http://placehold.it/300x400/333/fff
lorem.image('300x400', :random_color => true)
  #=> http://placehold.it/300x400/f47av7/9fbc34d
lorem.image('300x400', :text => 'blah')
  #=> http://placehold.it/300x400&text=blah
```

## カスタム定義ヘルパ

Middleman によって提供されるヘルパに加え, 自動的にコントローラやビューの中からアクセスできる独自のヘルパやクラスを追加することができます。

ヘルパメソッドを定義するには, `config.rb` の中で `helpers` ブロックを使います:

``` ruby
helpers do
  def some_method
    # ...do something here...
  end
end
```

また, ヘルパを含む外部の Ruby モジュールを作成し, 読込みもできます。 `lib` ディレクトリにファイルを配置します。例えば, `lib/custom_helpers.rb` という名前のファイルに上記のヘルパを抽出する場合には, モジュールを作成できます:

``` ruby
module CustomHelpers
  def some_method
    # ...do something here...
  end
end
```

次に `config.rb` に追加:

``` ruby
require "lib/custom_helpers"
helpers CustomHelpers
```

さらに簡単な方法は, `helpers` ディレクトリにヘルパを配置し, モジュールファイルに名前をつける方法です (例: `CustomHelpers` は `helpers/custom_helpers.rb` として配置)。 Middleman は自動的にファイルを読込み, ヘルパーとして受取ります。

[view the full documentation here]: http://www.padrinorb.com/guides/application-helpers
[Frank project]: https://github.com/blahed/frank
