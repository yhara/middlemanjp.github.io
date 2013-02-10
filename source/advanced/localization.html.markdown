---
title: 多言語化 (i18n)
---

# 多言語化 (i18n)

この拡張は `config.rb` に多言語化を有効化する API を提供します:

``` ruby
activate :i18n
```

デフォルトでは, この拡張は対応したい各ロケールを表す YAML ファイルをプロジェクトルートにある `locales` フォルダの中から探します。 YAML ファイルはサイトの中で多言語化する必要がある文字列毎のキーと値のセットです。キーは, テンプレートの中でこれらの文字列を参照するもので, ロケール毎に同じでなければなりませんが, その値は異なるでしょう。 2 つの YAML ファイル例です:

`locales/en.yml`:

``` yaml
---
en:
  hello: "Hello"
```

`locales/es.yml`:

``` yaml
---
es:
  hello: "Hola"
```

多言語化できるテンプレートは, デフォルトで `source/Localizable` フォルダの中に配置します (このオプションの変更方法はページ下部で) 。このフォルダにあるテンプレート毎に `I18n` ヘルパにアクセスできます。このヘルパを使うことで,  YAML ファイルからキーを参照し, 言語固有の値をテンプレートに差し込みます。簡単な `source/localizable/hello_world.html.erb` テンプレートの例です:

``` html
    <%= I18n.t(:hello) %> World
```

これは 2 つのファイルとして出力されます:

* /hello_world.html の内容は: "Hello World"
* /es/hello_world.html の内容は: "Hola World"

## URL パス

個々の言語にはそれ自身の名前空間のパスでアクセスすることができます。デフォルトでは, 第一言語はサイトのルートに配置されます (このオプションの変更方法はページ下部で) 。デフォルトのパスはパスの中でシンプルに言語名 ( YAML ファイル名) を使用します:

* /en/index.html
* /es/index.html
* /fr/index.html

`:path` オプションで変更できますが, 次のことを忘れないでください: URL は YAML ファイルの名前を含む:

``` ruby
activate :i18n, :path => "/langs/:locale/"
```

パスは次のように:

* /langs/en/index.html
* /langs/es/index.html
* /langs/fr/index.html

パスの一部に YAML ファイル名 を使いたくない場合, 違う値で書き換えることができます。

``` ruby
activate :i18n, :path => "/langs/:locale/", 
  :lang_map => { :en => :english, :es => :spanish, :fr => :french }
```

パスは次のように:

* /langs/english/index.html
* /langs/spanish/index.html
* /langs/french/index.html

## 多言語化パス

いくつかのケースでは, YAML ファイルの内容に加えることで, ファイル名を多言語化したい場合があります。言語固有の URL 書き換えを行うために YAML ファイルの中で特別な `paths` キーを使用できます。

`source/localizable/hello.html.erb` があるとします。デフォルトでは, 次のように出力されます:

* /hello.html
* /es/hello.html

スペイン語の場合のみ `hola.html` に書き換えたい場合, `locales/es.yml` の中で `paths` キーが使用出来ます:

``` yaml
---
es:
  hello: "Hola"
  paths:
    hello: "hola"
```

すると, 次のように出力されます:

* /hello.html
* /es/hola.html

## 多言語化するテンプレート

デフォルトでは, `source/localization` の中身が複数の言語で作成され, その他のテンプレートはそのまま作成されます。このフォルダの名前は変更することができます: `:templates_dir` オプション:

``` ruby
# 代わりに `source/language_specific` を探す
activate :i18n, :templates_dir => "language_specific"
```

## 手動で指定する言語

多言語化する言語のリストを指定する方を `locales/` フォルダのファイルを自動的に発見する方よりも好むのであれば,  `:langs` オプションを使用出来ます:

``` ruby
activate :i18n, :langs => [:en] # :en 以外のすべての言語を無視
```

## デフォルト (ルート) 言語

デフォルトでは, 第一言語 ( `:langs` で指定されず, `locales/` フォルダにあるもの) が "標準の" 言語になり, サイトのルートに配置されます。 2 つの言語が与えられた場合, `:en` で多言語化されたファイルがルートに配置されます:

* source/localizable/index.html.erb
  * build/index.html is english
  * build/es/index.html is spanish

`:mount_at_root` を使うことで, このデフォルトを変更したり, 特定の言語をルートに指定することを無効化できます:

``` ruby
activate :i18n, :mount_at_root => :es # スペイン語をルートに配置
# or
activate :i18n, :mount_at_root => false # すべての言語 URL に prefix がつく
```
