Middleman Guides
================

These are the source files that power the [Middleman guides site](http://middlemanapp.com/),
which is itself a [Middleman](https://github.com/middleman/middleman)-powered static page.

The guides are the primary documentation for Middleman, explaining the core features and extensions as well as pointing out community extensions.

Middleman is ever-vigilant against tag-soup, unreadable CSS and repetition. It stands-watch
over your Haml, Sass, and CoffeeScript producing only the cleanest and most efficient
markup.

## How to run the site locally

Make sure you have Ruby 1.9.3+ and Bundler installed, then clone this repo and run `bundle install`.
Then run `bundle exec middleman server` to start the server at `http://localhost:4567`

## Mailing List

If you have questions, answers can be found on our [community  forum](http://forum.middlemanapp.com/).

## Contributing

Please help Middleman flesh out its documentation. We'll merge in any reasonable
extensions to our website docs, and give credit for your help!

The guides are written in [Markdown](http://daringfireball.net/projects/markdown/).

## Donate

Please, [help support the Middleman team with a donation](https://spb.io/s/4dXbHBorC3).

## Copyright

Copyright (c) Thomas Reynolds. [Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/).

## 日本語版 Middleman Guides

このリポジトリは [Middleman Guides 日本語版](http://middlemanjp.github.io) のソースファイルです。  
オリジナルはこの README 上部記載 github リポジトリにあります。

### branch

Middleman Guides 日本語版は [Github Pages](http://pages.github.com/) を使って公開されているため, branch の扱いが通常のプロジェクトと少し異なります。

- master: Github Pages で公開される静的サイトのファイル
- localized: 翻訳された Middleman プロジェクトのファイル

### 作業方法

- `$ git checkout localized`
- `$ git fetch upstream` (upstream を指定していない場合には `$ git remote add git@github.com:middleman/middleman-guides.git` で追加)
- `$ git merge origin/master
- CONFLICT した部分を日本語化し修正する
- `$ git commit` (マージコミットのメッセージが自動入力される)
- `$ git push origin localized`

Middleman プロジェクトに対して push が完了した場合, `middleman build` したファイルを master ブランチに反映し push して Github Pages の内容を変更してください。

### 日本語化ルール

- 翻訳対象の行を翻訳した行を必ず合わせる (upstream に変更があった場合, CONFLICT させて翻訳箇所を特定させるため)
- できるだけ元の文章に基づいての日本語化が優先されるが, 日本語として不自然な場合または分かりづらい場合にはある程度の意訳で対応する

