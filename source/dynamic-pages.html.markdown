---
title: 動的ページ
---

# 動的ページ

## Proxy の定義

Middleman にはテンプレートファイルと 1 対 1 の関係を持っていないページを生成する機能があります。これが意味するのは, 変数に応じて複数のファイルを生成する 1 つのテンプレートを扱うことができます。プロキシを作成するには, `config.rb` で `proxy` メソッドを使用し, 作成したいパスを与え, 使用したいテンプレートのパスを与えます(テンプレートファイル自体の拡張子は除く)。 次は `config.rb` の設定例の 1 つです:

``` ruby
# source/about/template.html.erb が存在することを想定
["tom", "dick", "harry"].each do |name|
  proxy "/about/#{name}.html", "/about/template.html", :locals => { :person_name => name }
end
```

プロジェクトがビルドされる際に, ファイルは次のように出力されます:
When this project is built, four files will be output:

* '/about/tom.html' (テンプレートの中で `person_name` は "tom" として)
* '/about/dick.html' (テンプレートの中で `person_name` は "dick" として)
* '/about/harry.html' (テンプレートの中で `person_name` は "harry" として)
* '/about/template.html' (テンプレートの中で `person_name` は nil になる)

ほとんどの場合, `person_name` 変数なしにテンプレートを生成したくないでしょう。 Middleman にこれを無視するように命令できます:

``` ruby
["tom", "dick", "harry"].each do |name|
  proxy "/about/#{name}.html", "/about/template.html", :locals => { :person_name => name }, :ignore => true
end
```

`about/tom.html`, `about/dick.html` や `about/harry.html` だけが出力されます。

## 無視するファイル

`config.rb` に新しい `ignore` メソッドを追加し, サイト生成時に任意のパスを無視することも可能です。

``` ruby
ignore "/ignore-this-template.html"
```

正確なファイルパス, ファイル名のパターンマッチングや正規表現を ignore に与えることができます。
