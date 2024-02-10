# テーマの変更

## デフォルトテンプレートの上書き

Omeka Sには、すべてのテーマが使用し、デフォルトと異なるページ構造を望む場合に上書きするデフォルトテンプレートファイルのセットがあります。

デフォルトのテンプレートファイルは、Omeka Sのインストール内の`/application/view`フォルダにあり、以下のサブフォルダ内にあります: `/application/view/common`, `/application/view/error`, `/application/view/layout`, そして `/application/view/omeka/site`。テーマ作成者が必要とするほとんどのビューは、`/application/view/layout` および `/application/view/omeka/site`にあります。サブフォルダは、URLパターンに沿って表示されるページに対応しています。例えば、`{YourOmekaSSite}/item/show`に表示されるページは、`/application/view/omeka/site/item/show.phtml`のファイルによって生成されます。

テーマは、これらのファイルを上書きすることもすることもありません。例えば、デフォルトテーマには、デフォルトテンプレートの1つを上書きする`layout`ディレクトリがあります: `layout.phtml`。

- default/
    - view/
        - layout/
            - layout.phtml

テーマの中のファイルを変更したい場合、最初に見るべき場所はテーマ自身のディレクトリです。ただし、テーマがデフォルトテンプレートを上書きしている場合にのみそれは機能します。多くの場合、テーマにデフォルトテンプレートファイルをコピーする必要がありますが、ディレクトリ構造を維持するように注意してください。

例えば、アイテムのショーページとコレクションのブラウズページをデフォルトテーマで変更したいとします。

アイテムのショーページのためには、`/application/view/omeka/site/item/show.phtml`を`/default/view/omeka/site/item/show.phtml`にコピーする必要があります。

アイテムセットのブラウズページのためには、最初にディレクトリを作成する必要があります: `/default/view/omeka/site/item-set`

その後、`/application/view/omeka/site/item-set/browse.phtml`を`/default/view/omeka/site/item-set/browse.phtml`にコピーできます。

デフォルトテーマでの結果は以下のようになります::

- default/
      - view/
        - layout/
            - layout.phtml
        - omeka/
            - site/
                - item/
                    - show.phtml
                - item-set/
                    - browse.phtml
---

## テーマアセット

ビューファイル以外のテーマファイルは、テーマの`asset/`ディレクトリに種類別に分類されたサブディレクトリに収められています。典型的な`asset/`ディレクトリは、以下のようになります。

-   asset/
    - css/
    - sass/
    - js/
    - img/

ビュー内で`assets/`ディレクトリのファイルを参照するには、`$this->assetUrl('[subdirectory]/[filename.ext]')`を使用します。たとえば、ビュー内でテーマのロゴイメージを表示したい場合、以下のようになります。

`<img src="<?php echo $this->assetUrl('img/logo.jpg'); ?>" title="Logo">`

### カスタムCSSとJavascriptの使用

すべてのテーマにはそれぞれのversionのlayout.phtmlがあり、これは各ビューの構造を提供します。通常、含まれているのは以下のものです:

- ヘッダー,
- フッター,
- ページコンテンツの呼び出し (`<?php echo $this->content; ?>`),
- フォント、css、そしてjavascriptファイルへの呼び出し。

テーマはCSSとJavascriptファイルをZendのビューヘルパー機能([`headLink()`](https://docs.zendframework.com/zend-view/helpers/head-link/) と [`headScript()`](https://docs.zendframework.com/zend-view/helpers/head-script/))を使用してロードします。これらはOmeka Sのページとモジュールからのファイルと共に、これらのファイルがロードされる順番を制御するのに役立ちます。これらの関数はlayout.phtmlの`<head>`の前に表示されるべきです。

ここにテーマの`<head>`にこれらのファイルを含めるための例があります。

CSSの場合:

`$this->headLink()->prependStylesheet($this->assetUrl('css/style.css'));`

関数`prependStylesheet()`は、スタイルシートをロードされたCSSファイルのキューの先頭に配置します。

`$this->headLink()->appendStylesheet($this->assetUrl('css/style.css'));`

関数`appendStylesheet()`は、スタイルシートをロードされたCSSファイルのキューの末尾に配置します。

Javascriptの場合:

`$this->headScript()->prependFile($this->assetUrl('js/thedaily.js'));`

関数`prependFile()`は、JavascriptファイルをロードされたJavascriptファイルのキューの先頭に配置します。

`$this->headScript()->appendFile($this->assetUrl('js/thedaily.js'));`

関数`appendFile()`は、JavascriptファイルをロードされたJavascriptファイルのキューの末尾に配置します。

### ウェブフォントの使用

テーマ作成者は、ホステッドウェブフォントライブラリを介して使用するさまざまなフォントにアクセスできます。一般的な例には、[Google Fonts](https://fonts.google.com) や [Adobe Edge Web Fonts](https://edgewebfonts.adobe.com/) のような無料リソース、または [Fonts by Hoefler & Co](https://www.typography.com/webfonts/) や [Mosaic](https://www.monotype.com/fonts/mosaic) のような有料サブスクリプションサイトがあります。ホステッドウェブフォントの使用は、通常以下のように見えます：

`<link href="//fonts.googleapis.com/css?family=Lato" rel="stylesheet">`

この行は、Google Fontsがホスティングする“Lato”フォントファミリーを参照します。ライブラリのウェブフォントを使用するには、彼らが提供する同様のマークアップの行を探します。この行は、基本的にはホステッドされたフォントファイルをページにインポートするスタイルシートを呼び出すものです。したがって、テーマ作成者はlayout.phtmlでウェブフォントを含めるためにCSSファイルを含める機能を使用します。例えば：

`<?php $this->headLink()->prependStylesheet('//fonts.googleapis.com/css?family=Lato'); ?>`

## Omeka Sコンテンツを使った作業

Omeka Sのサイトは主に、Omeka Sリソース：アイテム、アイテムセット、メディアの表示に関するものです。メタデータとファイルがこれらのリソースに関連付けられて表示される方法をテーマは、リソース表現で定義されたメソッドを介して制御します。次のセクションでは、これらのメソッドを使った一般的なタスクについていくつかのハイライトを示し、より詳細なガイドは[Representations](../api/representations.md)で見つけることができます。

#### リソース画像の表示

Omeka Sリソースが関連する画像によって視覚的に表される場合、使用可能なサムネイルが選択されています。リソースのサムネイルにアクセスするには、以下のようになります：

`<?php echo $this->thumbnail($resource, 'thumbnailSize', [/* 属性の配列 */]); ?>`

これにより、主要メディアのデフォルトサムネイルを表示するHTMLが生成されます：

`<img src="http://yourdomain.com/omeka-s/files/medium/file.jpg" alt="画像の説明。">`

デフォルトサムネイル "medium" は、リソースに関連付けられるファイルがアップロードされたときにOmeka Sが生成するファイル派生物の1つです。これらのファイル派生物には、正方形のサムネイル、medium、largeがあります。render()関数に使用するファイル派生物、またはオリジナルファイルを定義できます。次の例では、サムネイルタイプが正方形のサムネイルに設定されています。

`<?php echo $this->thumbnail($resource, ['thumbnailType' => 'square']); ?>`

これらのファイル派生物の幅は`application/config/module.config.php`で定義されています。テーマ内の画像が予期されるサイズと異なる場合は、ほとんどの場合テーマのCSSが原因です。

[メディア固有のメソッドをもっと探る。](../api/representations.md#media-specific-methods)