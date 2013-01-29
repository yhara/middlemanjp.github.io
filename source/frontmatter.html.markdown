---
title: Frontmatter
---

# Frontmatter

Frontmatter は YAML 形式でテンプレート上部に含めることができるページ固有の変数です。

簡単な ERb テンプレートに, 固有のページのレイアウトを変更する Frontmatter を追加するとします。

``` html
---
layout: "custom"
my_list:
  - one
  - two
  - three
---

<h1>リスト</h1>
<ol>
  <% current_page.data.my_list.each do |f| %>
  <li><%= f %></li>
  <% end %>
</ol>
```

Frontmatter はテンプレートの最上部に配置し, 行頭から行末まで 3 つのハイフン `---` によって, その他の部分から分離されなければなりません。このブロックの中では, テンプレートの中で `current_page.data` ハッシュとして使用できるデータを作成することができます。 `layout` の設定は Middleman に直接渡され, レンダリングに使用されるレイアウトを変更します。同様の方法で ` ignore` や `directory_index` を設定することもできます。
