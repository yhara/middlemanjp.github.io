---
title: カスタム拡張
---

# カスタム拡張

Middleman の拡張は Middleman の様々なポイントにフックし, 新しい機能を追加し, コンテンツを操作する Ruby のクラスです。

最も基本的な拡張は次のようになります:

``` ruby
module MyFeature
  class << self
    def registered(app)
      
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

[`register`](http://rubydoc.info/github/middleman/middleman/master/Middleman/Extensions#register-class_method) メソッドは有効化する拡張の名前を選ばせます。拡張が有効になっているファイルのみ読込みたい場合はブロックを取ることができます。

`MyFeature` 拡張では, `registered` メソッドは `activate` コマンドが実行されるとすぐに呼び出されます。 `app` 変数は [`Middleman::Application`](http://rubydoc.info/github/middleman/middleman/master/Middleman/Application) クラスです。このクラスを使用すると, Middleman の環境を拡張することができます。

`activate` は拡張の設定のためにオプションのハッシュ (`register` に渡される) かブロックを渡すことができます。

``` ruby
module MyFeature
  # All the options for this extension
  class Options < Struct.new(:foo, :bar); end
  
  class << self
    def registered(app, options_hash={}, &block)
    options = Options.new(options_hash)
    yield options if block_given?
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

[`Middleman::Application`](http://rubydoc.info/github/middleman/middleman/Middleman/Application) クラス は拡張で使用されるグローバル設定 (`set` コマンドを使用する変数) を変更するために使用できます。

``` ruby
module MyFeature
  class << self
    def registered(app)
      app.set :css_dir, "lib/my/css"
    end
    alias :included :registered
  end
end
```

新しい設定を作成することができ, これは後で拡張の中でアクセスすることもできます。

``` ruby
module MyFeature
  class << self
    def registered(app)
      app.set :my_feature_setting, %w(one two three)
      app.send :include, Helpers
    end
    alias :included :registered
  end
  
  module Helpers
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
module MyFeature
  class << self
    def registered(app)
      app.extend ClassMethods
    end
    alias :included :registered
  end
  
  module ClassMethods
    def say_hello
      puts "Hello"
    end
  end
end
```

`Middleman::Application` クラスを拡張し, `app` として利用できるようにすることで, この環境に単に "Hello" を出力する `say_hello` メソッドを追加しました。内部的には, これらのメソッドはこのアプリの中で処理されるパスやリクエストのリストを作成するために使用されます。

## after_configuration コールバック

時には `config.rb` がコードを実行するために処理されるまで待ちたい場合があるでしょう。例えば, `:css_dir` 変数に依存していて, それがセットされるまで待ちたいような場合です。この実現のためコールバックを使用できます:

``` ruby
module MyFeature
  class << self
    def registered(app)
      app.after_configuration do
        the_users_setting = app.settings.css_dir
        set :my_setting, "#{the_users_setting}_with_my_suffix"
      end
    end
    alias :included :registered
  end
end
```

### Compass コールバック

同じように, Compass 内で用意される変数や設定に依存しているなら, `compass_config` コールバックを使用します。

``` ruby
module MyFeature
  class << self
    def registered(app)
      app.compass_config do |config|
        # config は Compass.configuration オブジェクト
        config.output_style = :compact
      end
    end
    alias :included :registered
  end
end
```

## ヘルパの追加

ヘルパはテンプレートの中で使用できるメソッドです。ヘルパメソッドを追加するには, 次にようにします:

``` ruby
module MyFeature
  class << self
    def registered(app)
      app.helpers HelperMethods
    end
    alias :included :registered
  end

  module HelperMethods
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

## リクエストコールバック

リクエストコールバックは Middleman がページをレンダリングする前に処理を行うことができます。これは別のソースからデータを返す場合や早い段階で失敗する場合に便利です。

例を示します:

``` ruby
module MyFeature
  class << self
    def registered(app)
      app.before do
        app.set :currently_requested_path, request.path_info
        true
      end
    end
    alias :included :registered
  end
end
```

上記はそれぞれのリクエストの開始時に `:currently_requested_path` の値を設定します。 "true" の返り値に注意してください。 `before_processing` を使うすべてのブロックは同じように true または false を返さなければなりません。

## サイトマップ拡張

サイトマップ拡張を作成することで [サイトマップ](/advanced/sitemap/) でページを変更したり追加したりできます。 [ディレクトリインデックス](/pretty-urls/) 拡張は通常のページをディレクトリインデックス版に再ルーティングするためにこの機能を使い, [ブログ拡張](/blogging/) はタグやカレンダーページを作成するためにいくつかプラグインを使っています。詳細は [`Sitemap::Store`](http://rubydoc.info/github/middleman/middleman/Middleman/Sitemap/Store#register_resource_list_manipulator-instance_method)。

``` ruby
module MyFeature
  class << self
    def registered(app)
      app.after_configuration do
        sitemap.register_resource_list_manipulator(
          :my_feature,
          MyFeatureManipulator.new(self),
          false
        )
      end
    end
    alias :included :registered
  end
  
  class MyFeatureManipulator
    def initialize(app)
      @app = app
    end
    
    def manipulate_resource_list(resources)
      resources.each do |resource|
         resource.destination_path.gsub!("original", "new")
      end
    end
  end
end
```

## after_build コールバック

このコールバックはビルドプロセスが完了した後にコードを実行するために使用されます。 [middleman-smusher] 拡張はビルド完了後に build フォルダの中のすべての画像を圧縮するためにこの機能を使用します。ビルド後に展開したスクリプトを結合することも考えることができます。

``` ruby
module MyFeature
  class << self
    def registered(app)
      app.after_build do |builder|
        builder.run my_deploy_script.sh
      end
    end
    alias :included :registered
  end
end
```

[`builder`](http://rubydoc.info/github/middleman/middleman/master/Middleman/Cli/Build) パラメータは CLI のビルドを実行するクラスで, そこから [Thor のアクション](http://rubydoc.info/github/wycats/thor/master/Thor/Actions) を使用できます。

[middleman-smusher]: https://github.com/middleman/middleman-smusher
