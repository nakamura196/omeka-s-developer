# スタイルガイド

このスタイルガイドはOmeka Sのテーマを書くために使用します。_[Githubスタイルガイド](http://primercss.io/)_ から多くを借りています。このガイドラインは作成中であり、そのため一部未完成な部分があります。

### 一般的なスタイル

* インデントにはスペース4つを使用してください。タブは使用しないでください。
* プロパティ宣言の後に`:`を記述する際はスペースを使用してください。
* ルール宣言の`{`の前にスペースを入れてください。
* rgbaを使用する場合を除き、カラーコードには`#000`のような16進数を使用してください。

`.css`ファイルに表示される見出しには`/*`スタイルのコメントを使用してください。Sass専用のセクション（たとえば`_base.scss`に設定されている変数）では、`//`コメント構文を使用してください。ヘッダーの頭に`!`を付けることで、Codaウェブエディタを使用しているテーマ作成者がセクションにブックマークを付ける際に役立ちます。

### _base.scss

`_base.scss`ファイルには、複数の`.scss`ファイルで使用される変数を含めるべきです。それ以外の場合は、それらが使用されるファイルの上部に変数が表示されます。

### _normalize.scss

私たちはリセットスタイルシートとしてNicolas Gallagherの[`normalize.css`](http://necolas.github.io/normalize.css/)を使用することを好みます。それを`.scss`ファイルとして含め、`_base.scss`にインポートしてください。