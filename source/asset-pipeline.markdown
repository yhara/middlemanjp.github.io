---
title: アセットパイプライン
---

## 依存性管理 (Sprockets)

[Sprockets] は Javascript (と CoffeeScript) のライブラリを管理するためのツールで, 依存性を宣言し, 3rd パーティのコードを含めます。Sprockets は .js や .coffee のファイルの中で,  `require` メソッドを使用できるようにし, プロジェクトまたは 3rd パーティ製の gem から外部ファイルを取り込むことができます。

jQuery ライブラリを含む `jquery.js` ファイルと別ファイルでアプリケーションコードが含まれる `app.js` があるとします。 次のようにすることで, app ファイルは jquery を含むことができます:

``` javascript
//= require "jquery"

$(document).ready(function() {
  $(".item").pluginCode({
    param1: true,
    param2: "maybe"
  });
});
```

このシステムは CSS ファイルの中でも機能します:

``` css
//= require "base"

body {
  font-weight: bold;
}

```


[Sprockets]: https://github.com/sstephenson/sprockets
