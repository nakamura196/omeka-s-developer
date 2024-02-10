# テーマ入門

Omeka Sはテーマをサポートしており、これはOmeka Sサイトのプレゼンテーションをカスタマイズする方法です。Omeka Sにはデフォルトのテーマが付属しており、[Center Row](http://github.com/omeka-s-themes/centerrow)、[Cozy](http://github.com/omeka-s-themes/cozy)、及び[The Daily](http://github.com/omeka-s-themes/thedaily)のテーマが公式にサポートされています。既存のテーマを変更する方法と新規テーマを一から始める方法についてのガイドは次の通りです。

## テーマの基本

Omeka Sのテーマは、Omeka Sインストールのルートディレクトリ内の`themes`ディレクトリに存在します。通常、以下のディレクトリとファイルが含まれます：

* `config/theme.ini`: これはOmeka Sにテーマが認識されるために**必須**のファイルです。テーマ名、作者、サポート情報、テーマバージョン、Omeka Sバージョン要件、ユーザー向けのテーマ設定オプションが含まれています。
* `asset`: テーマ作成者はこのディレクトリを使用して、CSS、Javascript、画像などのアセットを収容します。これは、Omeka Sインストールの`application/asset`のディレクトリ構造を模倣しています。
* `view`: このディレクトリ内のファイルはテーマの核心部分です。これらのカスタマイズされたテーマファイルは、`application/view/`にあるOmeka Sデフォルトビューテンプレートファイルに優先して適用されます。

## 既存のテーマを探索する

テーマに取り組む最良の方法の1つは、既存のテーマで実験することです。Omeka Sは公式にサポートされているテーマを通じて一貫したCSSセレクタのプールを使用するわけではありませんが、ほとんどのウェブブラウザは、与えられたサイトのHTMLとCSSを探索するための初心者向けのツールを提供しています。

[Google Chrome - ページとスタイルの検査と編集](https://developers.google.com/web/tools/chrome-devtools/inspect-styles/)

[Mozilla Firefox - CSSの調査と編集](https://developer.mozilla.org/ja/docs/Tools/Page_Inspector/How_to/Examine_and_edit_CSS)

[Apple Safari - 検査概要](https://support.apple.com/ja-jp/guide/safari-developer/inspecting-overview-dev1a8227029/mac)

## Sass

Omekaチームによって公式にサポートされているテーマ（[Omeka Sのデフォルトテーマ](http://github.com/omeka-s-themes/default)、[Center Row](http://github.com/omeka-s-themes/centerrow)、[Cozy](http://github.com/omeka-s-themes/cozy)、[The Daily](http://github.com/omeka-s-themes/thedaily)）はSASSを使用してCSSを生成しています。SASSの使用を始めたい方は、インストールのための推奨チュートリアルがこちらです。

* [Sass 基本](http://sass-lang.com/guide)
* [SASSとCSSの使用](sass_and_css.md)

プリプロセッサにまで踏み込むことなくCSSを編集したいだけの方は、'asset/sass'フォルダーを完全に無視して、.cssファイルに集中することができます。**注記：** .cssファイルを編集した後でSassを使うことにした場合は、初めてコンパイルする前にバックアップを取り、変更内容をメモしておくべきです。.scssファイルに加えた変更は.cssファイルに加えた変更を上書きします。