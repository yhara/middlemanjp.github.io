---
title: キャッシュ機能による改善
---

# キャッシュ機能による改善

可能な限り web サイトのレンダリングを高速化するには, JavaScript, CSS や画像のようなアセットファイルを [長時間キャッシュするように](https://code.google.com/speed/page-speed/docs/caching.html) web ブラウザに命令する適切なヘッダとともに配信すべきです。これはユーザがサイトに再度訪問する時 (またサイトのその他のページを訪問する時) これらのアセットファイルを再ダウンロードする必要がないことを意味します。長期間の `Expires` や `Cache-Control` のヘッダの設定は, アセットファイルを変更した時にユーザがまだキャッシュされたバージョンを使用していると問題を引き起こ可能性があります。 Middleman はこの問題の解決のために 2 つの方法を提供します。

## 一意の名前のアセットファイル

ユーザの古いファイルの使用防止に最も効果的な方法は, アセットファイルを変更する度にそのファイル名を変更するものです。手作業で行うには大変なので, Middleman にはこれに対応する `:asset_hash` 拡張が付属しています。まず, `config.rb` で拡張を有効化します:

``` ruby
activate :asset_hash
```

次に, 元のファイル名でアセットファイルを参照します。`image_tag` のようなヘルパを使用することができます。サイトのビルド時に, それぞれのアセットファイルは, そのファイルの内容で, 元のファイル名の終わりに余分なテキストを少し追加した名前で生成されます。他のファイル (HTML, CSS, JavaScript など) は元のファイル名の代わりに一意に生成されたファイル名を参照するように変更されます。"無期限" 指定でアセットを配信するようになりまが, アセットファイルを変更した場合には別のファイル名で表示されることを確認してください。

しかし, この拡張は名前を変えたアセットファイルを参照するようにファイルを書き換えて動作するので, 参照を失敗したり, コードで実行したくない何かをするかも知れません。この場合, 古いキャッシュを破壊するメソッドの使用を選択する必要があるかもしれません。

一部のファイルを名前の変更から除外したい場合, `:asset_hash` を有効化する時に `:ignore` オプションを渡し, 無視したいファイルを指す 1 つ以上のパターンマッチ, 正規表現や Proc を与えてください。同様に, ファイル拡張子をリネームし変更するために `:exts` オプションを渡すことができます。

## クエリ文字列によるキャッシュ破壊

2 つ目の方法はアセットファイルを参照する URL の末尾に値を追加する方法です。例えば, `my_image.png` を参照する代わりに, `my_image.png?1234115152` を参照します。URL 末尾の余分な情報は多くの (しかしすべてではない) ブラウザやプロキシに, 異なるキャッシュ破壊値をもつ同じファイルを別々にキャッシュするように命じるには充分です。これを使用するには, `config.rb` の中で `:cache_buster` 拡張を有効化します:

``` ruby
activate :cache_buster
```

Note このアプローチは, アセットファイルがよりキャッシュされないので好まれます。詳細は "["クエリ文字列によるキャッシュ破壊" トリックのテストに関するブログ記事](http://blog.solid1pxred.com/post/248906562/testing-ye-olde-querystring-cache-busting-trick)" で解説されています。

cache-safe な URL を使用するには, `image_path` や `javascript_include_tag` のような [アセットパスヘルパ](http://www.padrinorb.com/api/Padrino/Helpers/AssetTagHelpers.html) を使用しなければなりません。Sass ファイルの中でも (`image-url` など) [Compass ヘルパ](http://compass-style.org/reference/compass/helpers/urls/) を使用してください。 JavaScript の場合, `script.js.erb` のように ERb テンプレートを作成し, 正しい値を出力するように ERb のタグを使用してアセットヘルパを呼び出す必要があります。ユーザがファイルを取得しても (サーバ上のコピーは普通の名前なので) その変更が影響しない場合があることを忘れないでください。

## サーバの設定

長期間の `Expires` と `Cache-Control` ヘッダを使うためのサーバの設定は使用するサーバによって異なります。あなたの使用する特定のサーバの設定方法は Google の [page speed docs](https://code.google.com/speed/page-speed/docs/caching.html) を参照し, 設定が正しく行われているか確認するために [Google Page Speed](https://code.google.com/speed/page-speed/docs/extension.html) や [YSlow](https://addons.mozilla.org/en-US/firefox/addon/yslow/) を使用してください。
