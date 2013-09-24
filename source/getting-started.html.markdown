---
title: はじめに
---

# はじめに

Middleman はモダンな web 開発環境のすべてのショートカットやツールを使用して静的 web サイトを作成するためのコマンドラインツールです。

Middleman はコマンドラインを熟知していることを前提としています。 Ruby と web フレームワーク Sinatra がこのツールのベースになっています。この 2 つについて熟知していれば Middleman が存在しないかのように動作する理由を理解するには充分でしょう。

## インストール

Middleman は RubyGems のパッケージマネージャを使って配布されます。これは Middleman を使い始めるには Ruby のランタイムと RubyGems の両方が必要であることを意味します。

Mac OS X には Ruby と RubyGems の両方がパッケージされていますが, Middleman の依存ライブラリの一部はインストール時にコンパイルする必要があり OS X では Xcode を必要とします。 Xcode は [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?ls=1&mt=12) からインストールできます。もしくは, 無料の Apple Developer アカウントを持っていれば, この [ダウンロードページ](https://developer.apple.com/downloads/index.action) から Xcode 用のコマンドラインツールをインストールすることができます。

Ruby と RubyGems を起動し実行したら, コマンドラインから次のコマンドを実行します:

``` bash
gem install middleman
```

このコマンドは Middleman,  その依存ライブラリと Middleman を使うためのコマンドラインツールをインストールします。

このインストールプロセスはあなたの環境に 1 つの新しいコマンドと 3 つの便利な機能を追加します:

* middleman init
* middleman server
* middleman build

これらのコマンドの使用方法については以下で説明します。

## 新しいサイトの開発を始める: middleman init

開発を始めるには私たちは Middleman が拠点にするプロジェクトフォルダを作る必要があります。既存のフォルダを使用するか `middleman init` コマンドを使うことで Middleman がフォルダを作ることができます。

単に新しいサイト用のフォルダで実行されるコマンドを指し Middleman はそのフォルダにプロジェクトのスケルトンを作ります (またはフォルダを作ります)。

``` bash
middleman init my_new_project
```

### スケルトン

新しいプロジェクトごとに基本的な web 開発のスケルトンを作成します。この処理はすべてのプロジェクトで使用できるフォルダ階層とファイルの生成です。

真新しいプロジェクトには `source` フォルダと `config.rb` ファイルが含まれます。 source フォルダは web サイトを構築する場所です。スケルトンプロジェクトは javascript, CSS や画像のフォルダを含みますが, あなたの好みに合わせて変更することができます。

`config.rb` ファイルには Middleman の設定やコンパイル時の圧縮や "ブログモード" などの複雑な機能を有効化する方法がコメントアウトされたドキュメントとして含まれます。

#### Gemfile

Middleman は gem の依存関係の管理に Bundler の Gemfile に使えるように配慮します。新しいプロジェクトを作成すると, Middleman はあなたが使用している Middleman のバージョンを指定した Gemfile を生成します。これにより Middleman を特定のリリースシリーズに固定します (例えば 3.x シリーズ)。もちろん `Gemfile` の `:git` オプションを使用して Github から最新版の Middleman を使用することもできます。プロジェクトで使用するプラグインや追加ライブラリはすべて Gemfile にリストアップされるべきです。 起動時に Middleman はすべてのプラグインやライブラリを自動的に `require` します。 

#### config.ru

config.ru ファイルは Rack 対応の web サーバによってどのようにサイトが読み込まれるか記述します。このファイルは Middleman で作るサイトを開発時に Heroku のような Rack ベースのサーバにホスティングしたいユーザの便宜のために提供されています。しかし, Middleman は「静的」サイト生成のために構築されていることを忘れないでください。

あなたのプロジェクトの定型の `config.ru` ファイルを含めるには, init コマンドに `--rack` フラグを追加してください: 

``` bash
middleman init my_new_project --rack
```

### プロジェクトテンプレート

デフォルトの基本スケルトンに加え, Middleman は [HTML5 Boilerplate], [SMACSS], や [Mobile Boilerplate](http://html5boilerplate.com/mobile/) ベースのオプションテンプレートが付属します。Middleman 拡張 ([middleman-blog](/blogging/) のような) は同様に独自のテンプレートを使用することができます。テンプレート変更は `-T` や `--template` コマンドラインフラグを使用してアクセスできます。例えば, HTML5 Boilerplate ベースのプロジェクトを始める場合, このコマンドを使用します:

``` bash
middleman init my_new_boilerplate_project --template=html5
```

最後に, `~/.middleman/` フォルダの中にフォルダを作成することで独自のカスタムテンプレートのスケルトンを作成することができます。例えば, 私は `~/.middleman/mobile` フォルダを作成し, モバイルプロジェクトで使用する予定のファイルでそのフォルダを満たすことができます。

help フラグとともに middleman init コマンドを使用すると, すべての使用可能なテンプレートのリストが表示されます:

``` bash
middleman init --help
```

このコマンドは私の独自モバイルテンプレートを表示し, 私は以前と同じように新しいプロジェクトを作成できます:

``` bash
middleman init my_new_mobile_project --template=mobile
```
    
### 含まれるプロジェクトテンプレート

Middleman は基本的なプロジェクトテンプレートをいくつか搭載しています:

**[HTML5 Boilerplate]** 

``` bash
middleman init my_new_mobile_project --template=html5
```

**[SMACSS]**

``` bash
middleman init my_new_mobile_project --template=smacss
```

**[Mobile Boilerplate](http://html5boilerplate.com/mobile/)**

``` bash
middleman init my_new_mobile_project --template=mobile
```

### コミュニティプロジェクトテンプレート

こちらにもいくつか [コミュニティで開発されたテンプレート](http://directory.middlemanapp.com/#/templates/all) があります。

## 開発サイクル (middleman server)

Middleman はスタート時点から開発とプロダクションコードを分離します。これにより開発中にプロダクションでは不要または望ましくないツール群 ([Haml](http://haml-lang.com) や [Sass](http://sass-lang.com) などのような) を開発中に利用することができます。開発サイクルや静的サイトをこれらの環境に頼ります。

Middleman を使う時間の大半は開発サイクルになります。

コマンドラインを使い, プロジェクトフォルダの中からプレビューサーバを起動してください:

``` bash
cd my_project
bundle exec middleman server
```

このコマンドはローカル web サーバを起動します: `http://localhost:4567/`

`source` フォルダにファイルを作成編集し, プレビュー web サーバ上で反映された変更を確認することができます。

コマンドラインから `CTRL-C` を使ってプレビューサーバを停止できます。

### 飾りのない middleman コマンド

コマンドなしの `middleman` の使用はサーバの起動と同じです。

``` bash
bundle exec middleman
```

このコマンドは `middleman server` とまったく同じことを行います。

## 静的サイトのエクスポート (middleman build)

最後に, 静的サイトのコードを提供する準備ができているか "ブログモード" で静的ブログをホストする場合, サイトをビルドします。コマンドラインを使い, プロジェクトフォルダの中から `middleman build` を実行してください。

``` bash
cd my_project
bundle exec middleman build
```

このコマンドは `source` フォルダにあるファイル毎に静的ファイルを作成します。テンプレートファイルがコンパイルされ, 静的ファイルがコピーされ, 有効化されたビルド時の機能 (圧縮のような) が実行されます。 Middleman は自動的に前回のビルドから残っているがこれからは生成されないファイルを削除します。

[HTML5 Boilerplate]: http://html5boilerplate.com/
[SMACSS]: http://smacss.com/
