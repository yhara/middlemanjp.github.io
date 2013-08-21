---
title: カスタム拡張
---

# カスタム拡張

Middleman の拡張は Middleman の様々なポイントにフックし, 新しい機能を追加し, コンテンツを操作する Ruby のクラスです。このガイドは何が可能なのか説明しますが, すべてのフックや拡張ポイントを探すためには Middleman のソースや middleman-blog のようなプラグインのソースを読むべきです。

## 基本的な拡張

最も基本的な拡張は次のようになります:

``` ruby
class MyFeature < Middleman::Extension
    def initialize(app, options_hash={}, &block)
      super
    end
    alias :included :registered
  end
end

::Middleman::Extensions.register(:my_feature, MyFeature)
```

このモジュールは `config.rb` からアクセスできなければいけません。 `config.rb` に直接定義するか, 別の Ruby ファイルに定義し `config.rb` で `require` します。

最後に, モジュールを読み込んだら `config.rb` で有効化する必要があります:

``` ruby
activate :my_feature
```

[`register`](http://rubydoc.info/gems/middleman-core/Middleman/Extensions#register-class_method) メソッドは有効化する拡張の名前を選ばせます。拡張が有効になっているファイルのみ読み込みたい場合はブロックを取ることができます。

`MyFeature` 拡張では, `registered` メソッドは `activate` コマンドが実行されるとすぐに呼び出されます。 `app` 変数は [`Middleman::Application`](http://rubydoc.info/gems/middleman-core/Middleman/Application) クラスです。

`activate` は拡張の設定のためにオプションのハッシュ (`register` に渡される) かブロックを渡すことができます。`options` クラスメソッドでオプションを定義し `options` でアクセスすることができます:

``` ruby
class MyFeature < Middleman::Extension
  # All the options for this extension
  option :foo, false, 'Controls whether we foo'

  def initialize(app, options_hash={}, &block)
    super
    
    puts options.foo
  end
end

# 拡張を設定する 2 つの方法
activate :my_feature, :foo => 'whatever'
activate :my_feature do |f|
  f.foo = 'whatever'
  f.bar = 'something else'
end
```

`activate` へオプションを渡す方法は, 一般的に `set` を介した設定よりもグルーバル変数を設定することが好まれます (次のセクション参照) 。
    
## 変数の設定

[`Middleman::Application`](http://rubydoc.info/gems/middleman-core/Middleman/Application) クラス は拡張で使用されるグローバル設定 (`set` コマンドを使用する変数) を変更するために使用できます。

``` ruby
class MyFeature < Middleman::Extension
  def initialize(app, options_hash={}, &block)
    super
 
    app.set :css_dir, "lib/my/css"
  end
end
```

新しい設定を作成することができ, これは後で拡張の中でアクセスすることもできます。

``` ruby
class MyFeature < Middleman::Extension
  def initialize(app, options_hash={}, &block)
    super
   
    app.set :my_feature_setting, %w(one two three)
  end

  helpers do
    def my_helper
      my_feature_setting.to_sentence
    end
  end
end
```

`set` は `Middleman::Application` に新しいメソッドを追加します。 これは他の場所から `my_feature_setting` を介して変数の値を読み取ることができるということです。拡張に特定の値を必要とするだけの場合には, グローバル設定の代わりに `activate` のオプションを使うことを検討してください。

## config.rb にメソッドを追加

`config.rb` の中で利用できるメソッドは `Middleman::Application` の単なるクラスメソッドです。`config.rb` を使って新しいメソッドを追加してみましょう。

``` ruby
class MyFeature < Middleman::Extension
  def initialize(app, options_hash={}, &block)
    super
    app.extend ClassMethods
  end

  module ClassMethods
    def say_hello
      puts "Hello"
    end
  end
end
```

`Middleman::Application` クラスを拡張し, `app` として利用できるようにすることで, この環境に単に "Hello" を出力する `say_hello` メソッドを追加しました。内部的には, これらのメソッドはこのアプリの中で処理されるパスやリクエストのリストを作成するために使用されます。

## ヘルパの追加

ヘルパはテンプレートの中で使用できるメソッドです。ヘルパメソッドを追加するには, 次にようにします:

``` ruby
class MyFeature < Middleman::Extension
  def initialize(app, options_hash={}, &block)
    super
  end
  
  helpers do
    def make_a_link(url, text)
      "<a href='#{url}'>#{text}</a>"
    end
  end
end
```

テンプレートの中で, `make_a_link` メソッドにアクセスできるようになります。 ERb テンプレートでの使用例です:

``` html
<h1><%= make_a_link("http://example.com", "クリックしてください") %></h1>
```


## サイトマップ拡張

サイトマップ拡張を作成することで [サイトマップ](/advanced/sitemap/) でページを変更したり追加したりできます。 [ディレクトリインデックス](/pretty-urls/) 拡張は通常のページをディレクトリインデックス版に再ルーティングするためにこの機能を使い, [ブログ拡張](/blogging/) はタグやカレンダーページを作成するためにいくつかプラグインを使っています。詳細は [`Sitemap::Store`](http://rubydoc.info/gems/middleman-core/Middleman/Sitemap/Store#register_resource_list_manipulator-instance_method)。

``` ruby
class MyFeature < Middleman::Extension
  def initialize(app, options_hash={}, &block)
    super
  end

  def manipulate_resource_list(resources)
    resources.each do |resource|
      resource.destination_path.gsub!("original", "new")
    end
  end
end
```

## コールバック

Middleman には拡張によってフックできる部分があります。いくつか例を示しますが, ここに記述するよりも多くあります。

### after_configuration

コードを実行するために `config.rb` が読み込まれるまで待ちたい場合があります。例えば, `:css_dir` 変数に依存する場合, 設定されるまで待つべきです。次の例ではこのコールバックを使用しています:

``` ruby
class MyFeature < Middleman::Extension
  def initialize(app, options_hash={}, &block)
    super
  end
  
  def after_configuration
    the_users_setting = app.settings.css_dir
    app.set :my_setting, "#{the_users_setting}_with_my_suffix"
  end
end
```

### before

before コールバックは Middleman がページをレンダリングする直前に処理を実行することができます。別のソースからデータを返したり, 早期に失敗させる場合に便利です。

例:

``` ruby
class MyFeature < Middleman::Extension
  def initialize(app, options_hash={}, &block)
    super
    app.before do
      app.set :currently_requested_path, request.path_info
      true
    end
  end
end
```

この例では, リクエスト毎に `:currently_requested_path` に値をセットします。"true" を返すことに注意してください。`before` を使用したブロックは true または false を返さなければなりません。

### after_build

このコールバックはビルドプロセスが完了した後にコードを実行するために使用されます。 [middleman-smusher] 拡張はビルド完了後に build フォルダの中のすべての画像を圧縮するためにこの機能を使用します。ビルド後に展開したスクリプトを結合することも考えることができます。

``` ruby
class MyFeature < Middleman::Extension
  def initialize(app, options_hash={}, &block)
    super
    app.after_build do |builder|
      builder.run './my_deploy_script.sh'
    end
  end
end
```

[`builder`](http://rubydoc.info/gems/middleman-core/Middleman/Cli/Build) パラメータは CLI のビルドを実行するクラスで, そこから [Thor のアクション](http://rubydoc.info/github/wycats/thor/master/Thor/Actions) を使用できます。

### compass_config

同じく, 拡張が Compass が用意する変数や設定に依存する場合, `compass_config` コールバックが使用できます。

``` ruby
class MyFeature < Middleman::Extension
  def initialize(app, options_hash={}, &block)
    super
    
    app.compass_config do |config|
      # config is the Compass.configuration object
      config.output_style = :compact
    end
  end
end
```

[middleman-smusher]: https://github.com/middleman/middleman-smusher

